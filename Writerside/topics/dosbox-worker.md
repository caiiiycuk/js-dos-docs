# DOSBox Worker

DOSBox Worker is the worker version of the [DOSBox Direct](dosbox-direct.md) backend. It runs in its own browser thread,
so it never blocks the browser. **This version is recommended in most cases.**

You can create it like this:
```Javascript
const ci = await emulators.dosboxWorker(bundle);
```

Read [Estimating performance](estimating-performance.md) if you want to compare the performance of Direct and Worker versions.
