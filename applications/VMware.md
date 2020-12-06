## Zero out empty space

```bash
dd if=/dev/zero of=hugefile; sync; rm hugefile; sync
```

Then can defrag and compress via the VMware UI
