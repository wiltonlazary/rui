# Resource rules

Every command runs under a memory bound. Overlapping Chrome runs and a
runaway test growing off the JS heap can each exhaust the machine.

- Run every node command under a cap. A heap cap alone does not bound off-heap
  `ArrayBuffer` growth; `ulimit -v` makes a runaway fail within seconds with
  "Array buffer allocation failed":

  ```sh
  bash -c 'ulimit -v 6000000; NODE_OPTIONS="--max-old-space-size=1024 --disable-wasm-trap-handler" timeout 300 npm test'
  ```

  Use the same wrapper for builds, benchmarks and any other node command.
  `--disable-wasm-trap-handler` lets CanvasKit's wasm start under the address
  cap. Each WebAssembly memory still reserves its whole range up front
  (Taffy's about 4 GB, CanvasKit's 2 GB): a process that loads both caps them
  with `setFlagsFromString('--wasm-max-mem-pages=16384')` (node:v8) before
  loading (V8 flags are not allowed in NODE_OPTIONS), as ren-show's tests and
  ren-bench's Skia frame benchmark do.
- Run one heavy job at a time, machine-wide: a test suite, a build, a
  benchmark. Before each one, check `free -g` and `uptime`. Wait while less
  than 8 GB is available or the load is above 20. Hold the machine-wide lock
  around it (outside the Chrome lock, never inside), so sessions in every
  workspace take turns:

  ```sh
  flock -w 1800 "$HOME/.cache/heavy-job.lock" bash -c 'ulimit -v 6000000; NODE_OPTIONS="--max-old-space-size=1024 --disable-wasm-trap-handler" timeout 300 npm test'
  ```
- A job that dies without output, or a session that restarted, may be an OOM:
  check `journalctl -k | grep -i oom` before running it again.
- Take the machine-wide lock for every headless Chrome run, benchmarks and
  browser tests included, so only one Chrome runs at a time:

  ```sh
  flock "$HOME/.cache/chrome-bench.lock" <command>
  ```

  Chrome cannot start under `ulimit -v`, so give it a heap cap and a timeout
  instead.
- Record the load with every benchmark reading.
- Never `rm` with an unguarded variable glob. Keep scratch in the project's
  `artifact/tmp/<task>/`, never in tracked paths or outside the project.
