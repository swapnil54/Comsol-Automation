import mph
import time
import os
import multiprocessing as mp
from datetime import datetime

# ── Sweep configuration ──
sweep_config = [
    (1.28, 0.55, 0.65), (1.29, 0.55, 0.65), (1.30, 0.55, 0.65),
    (1.31, 0.55, 0.65), (1.32, 0.55, 0.65), (1.33, 0.55, 0.65),
    (1.34, 0.60, 0.75), (1.35, 0.60, 0.75), (1.36, 0.60, 0.75),
    (1.37, 0.65, 0.75), (1.38, 0.65, 0.80), (1.39, 0.75, 0.85),
    (1.40, 0.75, 0.95), (1.41, 0.85, 1.00), (1.42, 1.30, 1.75),
]

# ── Paths ──
BASE_MODEL  = r"G:\some comsol files\metal optimisation\metal 35nm\metal 35nm.mph"
RESULTS_DIR = r'G:\some comsol files\metal optimisation\metal 35nm\Results'
os.makedirs(RESULTS_DIR, exist_ok=True)

# Check which ones are already done
done = {f'RI_{n:.2f}.mph' for n in [t[0] for t in sweep_config] 
        if os.path.exists(os.path.join(RESULTS_DIR, f'RI_{n:.2f}.mph'))}

remaining = [t for t in sweep_config if f'RI_{t[0]:.2f}.mph' not in done]

print(f"Already done: {len(done)} | Remaining: {len(remaining)}")

if not remaining:
    print("All simulations completed!")
    exit()

def run_client(batch, client_id, port):
    print(f"\n{'#'*70}")
    print(f" CLIENT {client_id} STARTED (port {port}) - {len(batch)} simulations")
    print(f"{'#'*70}\n")

    try:
        client = mph.start(cores=2, port=port)
        for (n_ana, wl_start, wl_end) in batch:
            print(f"[ ] CLIENT {client_id} | n_ana = {n_ana:.2f} | λ {wl_start}–{wl_end} µm")
            model = client.load(BASE_MODEL)
            model.parameter('n_ana', str(n_ana))
            model.parameter('wl_start', str(wl_start))
            model.parameter('wl_end', str(wl_end))

            print(" → Solving Study 1...")
            start_time = time.time()
            model.solve('Study 1')
            duration = time.time() - start_time

            save_path = os.path.join(RESULTS_DIR, f'RI_{n_ana:.2f}.mph')
            model.save(save_path)

            print(f" → Done in {duration/60:.1f} min | Saved: RI_{n_ana:.2f}.mph")
            client.remove(model)
            print(" → Model cleared.\n")
            time.sleep(4)

        client.clear()
        print(f"✅ CLIENT {client_id} FINISHED")

    except Exception as e:
        print(f"❌ CLIENT {client_id} ERROR: {e}")


if __name__ == "__main__":
    print(f"🚀 Starting at {datetime.now().strftime('%H:%M:%S')}")
    print(f"   Running only remaining {len(remaining)} simulations with 5 clients\n")

    num_clients = 5
    batch_size = max(1, len(remaining) // num_clients)
    batches = [remaining[i:i + batch_size] for i in range(0, len(remaining), batch_size)]

    base_port = 2036
    ports = [base_port + i * 10 for i in range(num_clients)]

    with mp.Pool(processes=num_clients) as pool:
        pool.starmap(run_client, zip(batches, range(1, len(batches)+1), ports[:len(batches)]))

    print("\n" + "="*70)
    print("🎉 FINISHED! All remaining simulations completed.")
    print(f"   Results: {RESULTS_DIR}")
    print("="*70)
