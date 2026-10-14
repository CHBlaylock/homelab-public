# Homepage memory sizing — October 2026

Homepage's Proxmox container was initially allocated 512 MiB RAM. We temporarily increased it to 2 GiB to provide sufficient memory for the Node.js/pnpm/Next.js source build, which has higher peak requirements than serving the finished dashboard.

After the production build and dashboard integrations were working, runtime measurements showed about 173 MiB in use. We therefore restored the 512 MiB allocation to make roughly 1.5 GiB of configured memory capacity available to other workloads. After the change, the container reported about 151 MiB used, 360 MiB available, and 32 MiB swap in use; the Homepage systemd service remained active.

The lower allocation is intended for normal runtime, not necessarily future source builds. Rebuilding may require temporarily raising the memory limit again. Further dashboard and longer-term stability checks remain useful.
