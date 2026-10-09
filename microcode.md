# mROM

87 слов по 34 бита.

Поля микрокоманды, бит:

```text
addr_sel        1
mem_rd          1
mem_wr          1
alu_a           2
alu_b           3
alu_op          4
acc_sel         1
latch_ir        1
latch_ar        1
latch_dr        1
latch_acc       1
latch_pc        1
latch_flags     1
clr_v           1
clr_c           1
cond            4
upc_sel         1
target          7
halt            1
               --
               34
```

| maddr | метка | RTL | сигналы |
| ---: | --- | --- | --- |
| 0 | fetch | `IR ← M[PC][7:0]; AR ← PC + 1` | alu_a=PC, alu_b=ONE, alu_op=ADD, mem_rd, latch_ir, latch_ar |
| 1 |  | `mPC ← MAP_ROM[IR]` | upc_sel=MAP |
| 2 | taken | `PC ← DR; mPC ← fetch` | alu_a=ZERO, alu_b=DR, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 3 | halt | `halt` | halt |
| 4 | load_imm | `ACC ← M[AR]; PC ← PC + 5; mPC ← fetch` | addr_sel=AR, alu_a=PC, alu_b=FIVE, alu_op=ADD, acc_sel=MEM, cond=ALWAYS, mem_rd, latch_acc, latch_pc, target=0 |
| 5 | load_addr | `DR ← M[AR]; PC ← PC + 5` | addr_sel=AR, alu_a=PC, alu_b=FIVE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 6 |  | `AR ← DR` | alu_a=ZERO, alu_b=DR, alu_op=ADD, latch_ar |
| 7 |  | `ACC ← M[AR]; mPC ← fetch` | addr_sel=AR, acc_sel=MEM, cond=ALWAYS, mem_rd, latch_acc, target=0 |
| 8 | load | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 9 |  | `AR ← PC + SXT16(DR)` | alu_a=PC, alu_b=SXT16, alu_op=ADD, latch_ar |
| 10 |  | `ACC ← M[AR]; PC ← PC + 3; mPC ← fetch` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, acc_sel=MEM, cond=ALWAYS, mem_rd, latch_acc, latch_pc, target=0 |
| 11 | load_acc | `AR ← ACC` | alu_a=ACC, alu_b=ZERO, alu_op=ADD, latch_ar |
| 12 |  | `ACC ← M[AR]; PC ← PC + 1; mPC ← fetch` | addr_sel=AR, alu_a=PC, alu_b=ONE, alu_op=ADD, acc_sel=MEM, cond=ALWAYS, mem_rd, latch_acc, latch_pc, target=0 |
| 13 | store_addr | `DR ← M[AR]; PC ← PC + 5` | addr_sel=AR, alu_a=PC, alu_b=FIVE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 14 |  | `AR ← DR` | alu_a=ZERO, alu_b=DR, alu_op=ADD, latch_ar |
| 15 |  | `M[AR] ← ACC; mPC ← fetch` | addr_sel=AR, cond=ALWAYS, mem_wr, target=0 |
| 16 | store | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 17 |  | `AR ← PC + SXT16(DR)` | alu_a=PC, alu_b=SXT16, alu_op=ADD, latch_ar |
| 18 |  | `M[AR] ← ACC; PC ← PC + 3; mPC ← fetch` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, cond=ALWAYS, mem_wr, latch_pc, target=0 |
| 19 | store_ind | `DR ← M[AR]; PC ← PC + 5` | addr_sel=AR, alu_a=PC, alu_b=FIVE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 20 |  | `AR ← DR` | alu_a=ZERO, alu_b=DR, alu_op=ADD, latch_ar |
| 21 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 22 |  | `AR ← DR` | alu_a=ZERO, alu_b=DR, alu_op=ADD, latch_ar |
| 23 |  | `M[AR] ← ACC; mPC ← fetch` | addr_sel=AR, cond=ALWAYS, mem_wr, target=0 |
| 24 | add | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 25 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 26 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 27 |  | `ACC ← ACC + DR; V,C ← ALU; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=ADD, cond=ALWAYS, latch_acc, latch_flags, target=0 |
| 28 | sub | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 29 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 30 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 31 |  | `ACC ← ACC - DR; V,C ← ALU; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=SUB, cond=ALWAYS, latch_acc, latch_flags, target=0 |
| 32 | mul | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 33 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 34 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 35 |  | `ACC ← ACC * DR; V,C ← ALU; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=MUL, cond=ALWAYS, latch_acc, latch_flags, target=0 |
| 36 | div | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 37 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 38 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 39 |  | `ACC ← ACC / DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=DIV, cond=ALWAYS, latch_acc, target=0 |
| 40 | rem | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 41 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 42 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 43 |  | `ACC ← ACC % DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=REM, cond=ALWAYS, latch_acc, target=0 |
| 44 | and | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 45 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 46 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 47 |  | `ACC ← ACC & DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=AND, cond=ALWAYS, latch_acc, target=0 |
| 48 | or | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 49 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 50 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 51 |  | `ACC ← ACC \| DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=OR, cond=ALWAYS, latch_acc, target=0 |
| 52 | xor | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 53 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 54 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 55 |  | `ACC ← ACC ^ DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=XOR, cond=ALWAYS, latch_acc, target=0 |
| 56 | shiftl | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 57 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 58 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 59 |  | `ACC ← ACC << DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=SHL, cond=ALWAYS, latch_acc, target=0 |
| 60 | shiftr | `DR ← M[AR]; PC ← PC + 3` | addr_sel=AR, alu_a=PC, alu_b=THREE, alu_op=ADD, mem_rd, latch_dr, latch_pc |
| 61 |  | `AR ← SXT16(DR)` | alu_a=ZERO, alu_b=SXT16, alu_op=ADD, latch_ar |
| 62 |  | `DR ← M[AR]` | addr_sel=AR, mem_rd, latch_dr |
| 63 |  | `ACC ← ACC >> DR; mPC ← fetch` | alu_a=ACC, alu_b=DR, alu_op=SHR, cond=ALWAYS, latch_acc, target=0 |
| 64 | not | `ACC ← ~ACC` | alu_a=ACC, alu_b=DR, alu_op=NOT, latch_acc |
| 65 |  | `PC ← PC + 1; mPC ← fetch` | alu_a=PC, alu_b=ONE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 66 | clv | `PC ← PC + 1; V ← 0; mPC ← fetch` | alu_a=PC, alu_b=ONE, alu_op=ADD, cond=ALWAYS, latch_pc, clr_v, target=0 |
| 67 | clc | `PC ← PC + 1; C ← 0; mPC ← fetch` | alu_a=PC, alu_b=ONE, alu_op=ADD, cond=ALWAYS, latch_pc, clr_c, target=0 |
| 68 | jmp | `DR ← M[AR]; mPC ← taken` | addr_sel=AR, cond=ALWAYS, mem_rd, latch_dr, target=2 |
| 69 | beqz | `DR ← M[AR]; if Z: mPC ← taken` | addr_sel=AR, cond=Z, mem_rd, latch_dr, target=2 |
| 70 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 71 | bnez | `DR ← M[AR]; if NZ: mPC ← taken` | addr_sel=AR, cond=NZ, mem_rd, latch_dr, target=2 |
| 72 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 73 | bgtz | `DR ← M[AR]; if GTZ: mPC ← taken` | addr_sel=AR, cond=GTZ, mem_rd, latch_dr, target=2 |
| 74 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 75 | bgez | `DR ← M[AR]; if GEZ: mPC ← taken` | addr_sel=AR, cond=GEZ, mem_rd, latch_dr, target=2 |
| 76 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 77 | bltz | `DR ← M[AR]; if LTZ: mPC ← taken` | addr_sel=AR, cond=LTZ, mem_rd, latch_dr, target=2 |
| 78 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 79 | bvs | `DR ← M[AR]; if V: mPC ← taken` | addr_sel=AR, cond=V, mem_rd, latch_dr, target=2 |
| 80 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 81 | bvc | `DR ← M[AR]; if NV: mPC ← taken` | addr_sel=AR, cond=NV, mem_rd, latch_dr, target=2 |
| 82 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 83 | bcs | `DR ← M[AR]; if C: mPC ← taken` | addr_sel=AR, cond=C, mem_rd, latch_dr, target=2 |
| 84 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
| 85 | bcc | `DR ← M[AR]; if NC: mPC ← taken` | addr_sel=AR, cond=NC, mem_rd, latch_dr, target=2 |
| 86 |  | `PC ← PC + 5; mPC ← fetch` | alu_a=PC, alu_b=FIVE, alu_op=ADD, cond=ALWAYS, latch_pc, target=0 |
