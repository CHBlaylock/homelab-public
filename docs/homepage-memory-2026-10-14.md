# Homepage memory sizing — October 2026

Homepage's Proxmox container was initially allocated 512 MiB RAM. We temporarily increased it to 2 GiB to provide sufficient memory for the Node.js/pnpm/Next.js source build, which has higher peak requirements than serving the finished dashboard.

After the production build and dashboard integrations were working, runtime measurements showed about 173 MiB in use. We therefore restored the 512 MiB allocation to make roughly 1.5 GiB of configured memory capacity available to other workloads. After the change, the container reported about 151 MiB used, 360 MiB available, and 32 MiB swap in use; the Homepage systemd service remained active.

The lower allocation is intended for normal runtime, not necessarily future source builds. Rebuilding may require temporarily raising the memory limit again. Further dashboard and longer-term stability checks remain useful.

## Final dashboard verification

After returning the container to 512 MiB RAM, the dashboard loaded successfully and all four live widgets displayed data: Pi-hole query/block statistics, Proxmox resource and guest counts, Uptime Kuma 10 sites up and 0 down, and Mealie recipe statistics. The personal-cloud navigation links remained visible. The dashboard displayed approximately 352 MB free memory and 3% CPU at the time of the screenshot. This validates immediate normal operation, not sustained performance under all workloads.

## Memory-pressure and swap verification

At the 512 MiB runtime limit, `/proc/pressure/memory` showed `some` and `full` avg10/avg60/avg300 all at 0.00, indicating no recent memory pressure during measurement. `vmstat 1 5` showed zero swap-in and swap-out in all four interval samples; roughly 31 MiB of swap remained occupied from earlier activity. CPU was 95–100% idle in those samples. Decision: retain 512 MiB RAM and 512 MiB swap for current production use. This is a point-in-time observation; monitor if workload or build activity changes.
