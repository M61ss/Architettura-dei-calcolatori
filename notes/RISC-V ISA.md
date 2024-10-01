# RISC-V ISA

## Operandi

Il RISC-V contiene 32 registri per un rapido accesso ai dati. Per poter eseguire le operazioni, il RISC-V necessita che gli operandi si trovino nei registri. Il registro `x0` contiene sempre il valore `0`.

Il RISC-V può accedere alla memoria solo tramite istruzioni di trasferimento dati (dalla memoria ai registri o viceversa). Esso utilizza l'indirizzamento a byte e variabili a 32 o 64 bit, perciò due variabili in serie distano rispettivamente 4 o 8 byte in memoria.
\
Il RISC-V a 32 bit può gestire 2^32 byte di memoria, ovvero 2^30 parole della dimensione di 4 byte l'una.

## Operazioni aritmetiche - somma e sottrazione

```assembly
add x5, x6, x7
sub x5, x6, x7
```

Nel registro `x5` viene scritto il risultato del contenuto di `x6` sommandogli (`add`) o sottraendogli (`sub`) il contenuto di `x7`.

**Design principle**: _simplicity favours regularity_

### Operatori immediati o costanti

Spesso i programmi usano all'interno di un'operazione un costante per, ad esempio, incrementare l'indice di un array che si sta scorrendo.

Esiste nel RISC-V la possibilità di usare l'istruzione immediata `addi` (add immediate):

```assembly
addi x22, x22, 4
```

Nell'operazione di esempio riportata di sopra si aggiunge `4` al contenuto del registro `x22`. Lavorando a 32 bit questa operazione corrisponde al passaggio alla parola successiva.

**Design principle**: _make the common case fast_

> [!IMPORTANT] Migliorare le performance
>
> Usare le istruzioni immediate permette di ridurre le operazioni da eseguire e richiede meno energia perché non è necessario mantenere la costante in memoria.

> [!IMPORTANT] Negare il contenuto di un registro
>
> Si può negare il contenuto di un registro usando `sub` con il contenuto del registro `x0` (che è sempre `0`) come primo operando.

## Words

Chiameremo "**word**" una parola a 32 bit, "**double word**" una parola a 64 bit.

## Registri

I nomi dei registri del RISC-V sono composti da una `x` seguita da un numero da 0 a 31.

Elenco dei registri e il rispettivo contenuto:

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

> [!IMPORTANT] Numero di registri
> Un basso numero di registri corrisponde a una maggiore velocità del ciclo di clock.

**Design principle**: _smaller is better_

## Interazione con la memoria

Come abbiamo detto, per eseguire operazioni aritmetiche il RISC-V richiede che i dati siano sui registri. Per questa ragione esistono le istruzioni di trasferimento dati. 

La memoria viene vista dal RISC-V come un grande vettore monodimensionale. Ad essa è possibile accedervi utilizzando un indirizzo che stabilisce in quale cella recuperare il dato. 
\
**La numerazione degli indirizzi parte da 0.**

Nel RISC-V la memoria è **byte-addressed**, ovvero ogni indirizzo identifica un byte. Ciò significa che sommare 1 a un indirizzo vuol dire passare al byte di memoria successivo a quello considerato.

Il RISC-V è **little endian**, quindi il byte meno significativo di una parola si trova all'indirizzo più piccolo.

Per accedere a una parola in memoria, l'istruzione deve fornire l'indirizzo di memoria corrispondente al byte in cui essa ha inizio.

### Spostare dati

L'istruzione che sposta un dato dalla memoria a un registro si chiama "**load**".
Nel RISC-V si chiama `lw` (load word) e la sua sintassi è la seguente:

```assembly
lw x5, 40(x6)
```

`lw` salva nel registro `x5` il dato che recuperiamo dalla memoria all'indirizzo `x6` + `40`.
\
L'indirizzo del dato in memoria viene ottenuto dalla costante, detta "**offset**", sommata al contenuto del registro con cui essa è in coppia, detto "**registro base**".

L'istruzione opposta è detta "**store**". Trasferisce il contenuto di un registro in memoria. Nel RISC-V si chiama `sw` (store word) e ha la sintassi:

```assembly
sw x5, 40(x6)
```

`sw` scrive in memoria all'indirizzo `x6` + `40` il valore contenuto nel registro `x5`.

> [!TIP] 
>
> Lavorare con la memoria significa eseguire tante operazioni di load/store. Per questo motivo i compilatori cercano di usare i registri il più possibile.
