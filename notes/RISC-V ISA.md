# RISC-V ISA <!-- omit from toc -->

- [Operandi](#operandi)
- [Operazioni aritmetiche - somma e sottrazione](#operazioni-aritmetiche---somma-e-sottrazione)
  - [Operatori immediati o costanti](#operatori-immediati-o-costanti)
- [Words](#words)
- [Registri](#registri)
- [Interazione con la memoria](#interazione-con-la-memoria)
  - [Spostare dati](#spostare-dati)
- [Rappresentazione dei numeri](#rappresentazione-dei-numeri)
  - [Complemento a 2](#complemento-a-2)
  - [Estensione del segno](#estensione-del-segno)
- [Rappresentazione delle istruzioni](#rappresentazione-delle-istruzioni)
  - [Formato di tipo R (register)](#formato-di-tipo-r-register)
  - [Formato di tipo I (immediate)](#formato-di-tipo-i-immediate)
  - [Formato di tipo S (store)](#formato-di-tipo-s-store)
  - [Formato delle operazioni di shift](#formato-delle-operazioni-di-shift)
- [All instructions](#all-instructions)
  - [Math](#math)
  - [Logical](#logical)
  - [Load](#load)

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

## Rappresentazione dei numeri

In uno spazio di rappresentazione a 32 bit si compongono numeri positivi da 0 a 2^31-1, riservando quindi il bit più significativo alla rappresentazione del segno: i numeri negativi hanno il bit più significativo pari a 1.
\
In questo modo si ottiene un range di valori che, più in generale, si estende da -2^(n-1) a 2^(n-1) - 1.

> [!WARNING] 
>
> 2^(n-1) non ha il suo corrispondente positivo. La ragione sta nel fatto che nel conteggio dei numeri positivi si include anche lo 0.

### Complemento a 2

Per effettuare correttamente le operazioni aritmetiche è necessario rappresentare i numeri negativi in complemento a 2 (si ricorda che il calcolatore è in grado di fare soltanto le somme).

Per eseguire il complemento a 2 bisogna:
- Invertire tutti i bit del numero positivo, trasformando gli 1 in 0 e viceversa.
- Sommare 1 al numero risultante.

### Estensione del segno

Nel caso in cui desideri rappresentare un numero inizialmente codificato a n bit in una codifica con più bit devo replicare il bit di segno a sinistra.

_Esempio_:

In 8 bit:

+2 = **0**000 0010
\
-2 = **1**111 1110

Diventa in 16 bit:

+2 = **0000 0000 0**000 0010
\
-2 = **1111 1111 1**111 1110

## Rappresentazione delle istruzioni

Tutte le istruzioni del RISC-V sono codificate come parole a 32 bit aventi uno specifico formato.

### Formato di tipo R (register)

Questo formato viene usato per le istruzioni di registro.

![instruction_sample_r](..\resources\instruction_sample_r.png)

Campi dell'istruzione:

- opcode (operative code): definisce l'operazione base dell'istruzione.
- rd (destination registry): registro di destinazione del risultato.
- funz3: codice operativo aggiuntivo a 3 bit.
- rs1 (source register 1): registro sorgente per il primo operando.
- rs2 (source register 2).
- funz7: codice operativo aggiuntivo di 7 bit.

### Formato di tipo I (immediate)

Questo formato viene usato per le operazioni immediate o richiedenti costanti. 

![instruction_sample_i](..\resources\instruction_sample_i.png)

Campi dell'istruzione:

- opcode (operative code): definisce l'operazione base dell'istruzione.
- rd (destination registry): registro di destinazione del risultato.
- funct3: codice operativo aggiuntivo a 3 bit.
- rs1 (source register 1): registro sorgente per il primo operando.
- immediate: operando costante oppure offset da aggiungere all'indirizzo di base.

### Formato di tipo S (store)

Questo formato viene usato per le operazioni di salvataggio in memoria.

![instruction_sample_s](..\resources\instruction_sample_s.png)

- opcode (operative code): definisce l'operazione base dell'istruzione.
- funct3: codice operativo aggiuntivo a 3 bit.
- rs1 (source register 1): registro che contiene l'indirizzo di memoria di base.
- rs2 (source register 2): registro che contiene l'operando che deve essere salvato in memoria.
- immediate: operando costante oppure offset da aggiungere all'indirizzo di base, composto dai due blocchi bianchi (7+5 bit).

### Formato delle operazioni di shift

Questo formato viene usato per le operazioni di shift dei bit.

![instruction_sample_shift](..\resources\instruction_sample_shift.png)

- opcode (operative code): definisce l'operazione base dell'istruzione.
- rd (destination registry): registro di destinazione del risultato.
- funct3: codice operativo aggiuntivo a 3 bit.
- rs1 (source register 1): registro che contiene l'indirizzo di memoria di base.
- immed: definisce di quante posizioni deve essere eseguito lo shift.
- funct6: codice operativo aggiuntivo di 6 bit.












## All instructions

### Math

- `add`: somma.
- `sub`: sottrazione.
- `addi`: somma istantanea.

### Logical

- `slli`: shift left per `i` bit.
- `srli`: shift right.
- `and`, `andi`: and bit a bit.
- `or`, `ori`: or bit a bit.
- `xor`, `xori`: xor/not bit a bit.

### Load

- `lw`: spostamento di parola.
- `lh`: spostamento di mezza parola.
- `lb`: spostamento di byte.
- `lwu`, `lhu`, `lbu`: analoghi rispettivamente ai 3 precedenti, ma unsigned.
