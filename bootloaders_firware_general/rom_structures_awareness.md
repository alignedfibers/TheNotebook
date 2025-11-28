

### 1. True dual ROM chips (“backup BIOS” style)

Two physical SPI chips or two clearly separated descriptor regions, where the bootblock decides which one to trust. Typical behavior:

* Main ROM = active
* Backup ROM = recovery/failsafe
* A failed update, checksum error, or jumper keypress causes a fallover to the backup.

If your laptop does this, then your live chainload / embed phase needs to answer:

* Which chip / bank did I actually boot from?
* Is the “other” bank still stock firmware that can recover me if I brick this one?

That’s where flashrom dumps and size comparisons help: two different contents, same size, slightly offset mappings, etc.

### 2. A/B firmware “banks” inside one chip

This is closer to Android’s A/B slots idea: one SPI flash, partitioned into “A” and “B” firmware images, plus a little bit of logic that flips a flag for which one to boot. Vendor updates often:

* Write the new image into the inactive slot,
* Flip a boot flag,
* Fall back to the old slot if boot fails.

If your HP is doing that, then the pipeline you sketched becomes:

1. Boot from A, chainload your lab payload.
2. From that environment, inspect ROM to identify A vs B images.
3. Embed your coreboot-based payload into the *inactive* bank.
4. Flip whatever flag/variable marks that bank active.
5. Reboot and see if it comes up; in worst case, system may flip back.

That’s kind of the “best case” for your idea, because it means:

* You can experiment in the inactive bank while the active stays untouched,
* Your “live system staging lab” doesn’t necessarily have to risk the fallback path.

---

If you want, next step I can sketch a tiny “A/B-aware” version of your process for the notebook:

> hook → chainload → detect A/B → analyze active/inactive images → embed into inactive → flip → test → promote

so future-you remembers to include the A/B detection step before doing any hotflash-style embedding.
