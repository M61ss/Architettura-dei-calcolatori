# RISC-V ISA

## Operandi

Il RISC-V contiene 32 registri per un rapido accesso ai dati. Per poter eseguire le operazioni, il RISC-V necessita che gli operandi si trovino nei registri. Il registro x0 contiene sempre il valore `0`.

Il RISC-V può accedere alla memoria solo tramite istruzioni di trasferimento dati (dalla memoria ai registri o viceversa). Esso utilizza l'indirizzamento a byte e variabili a 32 o 64 bit, perciò due variabili in serie distano rispettivamente 4 o 8 byte in memoria.
\
Il RISC-V a 32 bit può gestire 2^32 byte di memoria, ovvero 2^30 parole della dimensione di 4 byte l'una.

## Operazioni aritmetiche

### Somma e sottrazione

```assembly
add x5, x6, x7
sub x5, x6, x7 
```

Nel registro `x5` viene scritto il risultato del contenuto di `x6` sommandogli (`add`) o sottraendogli (`sub`) il contenuto di `x7`.

Nel caso si desideri fare operazioni con un valore del registro e un **valore costante**, quest'ultimo deve obbligatoriamente trovarsi in ultima posizione.

```assembly
add x5, x6, 20
sub x5, x6, 20
```

**Design principle**: simplicity favours regularity

## Words

Una **word** è una parola a 32 bit. Una **double word** è una parola a 64 bit.

## Registri

I nomi dei registri del RISC-V sono composti da una `x` seguita da un numero da 0 a 31.

Elenco dei registri e del rispettivo contenuto:

- `x0`: valore costante 0.
- `x1`: return address.
- `x2`: stack pointer.
- `x3`: global pointer.
- `x4`: thread pointer.
- `x5-x7`, `x28-x31`: temporaries.
- `x8`: frame pointer.
- `x9`, `x18-x27`: saved registers.
- `x10`, `x11`: function arguments/results.
- `x12-x17`: function arguments.

**Design principle**: smaller is better
