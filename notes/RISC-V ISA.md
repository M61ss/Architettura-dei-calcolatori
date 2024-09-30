# RISC-V ISA

## Operandi

Il RISC-V contiene 32 registri per un rapido accesso ai dati. Per poter eseguire le operazioni, il RISC-V necessita che gli operandi si trovino nei registri. Il registro x0 contiene sempre il valore `0`.

Il RISC-V può accedere alla memoria solo tramite istruzioni di trasferimento dati (dalla memoria ai registri o viceversa). Esso utilizza l'indirizzamento a byte e variabili a 32 o 64 bit, perciò due variabili in serie distano rispettivamente 4 o 8 byte in memoria.
\
Il RISC-V a 32 bit può gestire 2^32 byte di memoria, ovvero 2^30 parole della dimensione di 4 byte l'una.

## Operazioni aritmetiche

### Somma e sottrazionee

```assembly
add x5, x6, x7
sub x5, x6, x7 
```

Nel registro `x5` viene scritto il risultato del contenuto di `x6` sommandogli (`add`) o sottraendogli (`sub`) il contenuto di `x7`.
