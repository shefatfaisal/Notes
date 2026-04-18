cpu has 4 units
- cu(control unit)
- execution unit(eu)
- registers
- flags

### 1) Control Unit (CU)

The **"manager"** of the CPU. It doesn't do any actual calculation itself — instead it **coordinates everything**.

- Fetches the next instruction from RAM
- **Decodes** it (figures out _what_ the instruction is asking)
- Tells the other parts what to do
- Controls the flow of data **between** memory, registers, and the execution unit

> Think of it like a **conductor of an orchestra** — it doesn't play any instrument, but nothing works without it.

### 1) Control Unit (CU)

The **"manager"** of the CPU. It doesn't do any actual calculation itself — instead it **coordinates everything**.

- Fetches the next instruction from RAM
- **Decodes** it (figures out _what_ the instruction is asking)
- Tells the other parts what to do
- Controls the flow of data **between** memory, registers, and the execution unit

> Think of it like a **conductor of an orchestra** — it doesn't play any instrument, but nothing works without it.

### 3) Registers

These are **tiny, ultra-fast memory slots** built directly inside the CPU.

- Much faster than RAM — no need to go outside the CPU
- Hold data that is **currently being used** or just produced
- Examples in x86-64: `RAX`, `RBX`, `RCX`, `RDX`
- Also include special registers like:
    - **IP (Instruction Pointer)** — tracks which instruction runs next
    - **SP (Stack Pointer)** — tracks the top of the stack

> Think of them as a **notepad on your desk** — small, but instantly accessible.

---

### 4) Flags

Flags are **1-bit indicators** that get set automatically after an instruction executes, signaling what happened.

Common flags:

|Flag|Name|Meaning|
|---|---|---|
|`ZF`|Zero Flag|Result was zero|
|`CF`|Carry Flag|An overflow/carry occurred|
|`SF`|Sign Flag|Result was negative|
|`OF`|Overflow Flag|Signed overflow occurred|

These flags are stored together in a special register called the **Flags Register** (or `EFLAGS`/`RFLAGS` in x86).


