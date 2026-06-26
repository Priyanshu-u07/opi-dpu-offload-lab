# Lab C `make test` PASS (opiproject/dpu-operator  main)

**Result:** Ginkgo ran 5 suites, **Test Suite Passed, exit 0**, ~26 min, composite coverage 44.7%.

**Note on environment:** a first rootless run passed 5/6 specs but failed the
Controller suite's `AfterAll` teardown (`kindcluster.go:110: permission denied`)
 the kind node's kubelet files are root-owned and an unprivileged user can't
delete them in a nested rootless Codespace. Re-running rootful (`sudo`) lets the
teardown succeed, yielding a clean exit 0. The suite also flake-retries the
Controller suite (up to 5x), which is why the run took ~26 min.
