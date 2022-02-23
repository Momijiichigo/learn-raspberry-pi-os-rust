## Memo

This is for organizing my understanding on this project structure. 

Might be wrong.

### Vocab

- BSP: Board Support Package.
  - contains target board specific definitions and functions.
- 
---

`TARGET=aarch64-unknown-none-softfloat`

`KERNEL_BIN=./kernel8.img`

```sh
RUSTFLAGS="-C link-arg=-Tsrc/bsp/raspberrypi/link.ld -C target-cpu=cortex-a53 -D warnings -D missing_docs

cargo rustc --target=$(TARGET) --features bsp_rpi3 --release
```

```toml
[[bin]]
name = "kernel"
path = "src/main.rs"
```
-> ELF: `target/$(TARGET)/release/kernel`

```mermaid
sequenceDiagram 

Makefile ->> Cargo: build
Note right of Cargo: linker tells the <br> entry address (.) 
Note right of Cargo: ELF to <br>./target/aarch~/release/kernel
Cargo ->> File: 
Makefile ->> File: generate ./kernel8.img
Note right of Cargo: remove all symbols <br> and non-alloc sections <br> from the ELF and output to <br> KERNEL_BIN (= ./kernel8.img )
Makefile ->> Docker: create docker container
Note left of Docker: start QEMU <br>inside the docker <br> using ./kernel8.img
File ->> Docker: use kernel8.img
```

```mermaid
graph TB

A(main) -->B(bsp)
A --> cpu --> boot -->|./src/_arch/aarch64/cpu/boot.rs| arch_boot
--> boot.s -->|has entry point: _start| C(Loop with wfe: <br>wait for event, halting)

A -->|needed for compile| panic_wait
```