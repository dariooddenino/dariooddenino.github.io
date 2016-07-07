---
layout:     post
title:      Functor, Applicative e Monad illustrati
date:       2016-07-07 09:32:11
summary:    
categories: haskell
---

## Introduzione

Alcuni concetti possono risultare difficilmente intuibili o sembrare troppo
astratti. Functor, Applicative e Monad vi sembreranno incredibilmente semplici
dopo aver letto la traduzione di [questo](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html) fantastico post!

Gli esempi sono scritti in Haskell, ma i concetti sono generici e possono essere
applicati in vari ambiti.

## Cominciamo!

Questo &egrave; un semplice valore:

![value]({{ site.url }}/images/value.png)

E sappiamo come applicare una funzione a questo valore:

![value_apply]({{ site.url }}/images/value_apply.png)

Molto semplice. Estendiamo questo concetto dicendo che un qualunque valore pu&ograve;
trovarsi all'interno di un contesto. Per ora puoi immaginare un contesto come una scatola
al cui interno puoi inserire un valore:

![value_and_context]({{ site.url }}/images/value_and_context.png)

A questo punto, applicando una funzione a questo valore otterrai risultati
diversi **a seconda del contesto**. Questa &egrave; l'idea su cui Functor, 
Applicative, Monad, Arrow, etc. sono basati. Il data type `Maybe` definisce
due contesti correlati:

![context]({{ site.url }}/images/context.png)

```haskell
data Maybe a = Nothing | Just a
```

In un momento vedremo come l'applicazione di una funzione differisce quando
siamo all interno di `Just a` piuttosto che di `Nothing`. Prima parliamo dei Functor!

### Functor

Quando un valore &egrave; all'interno di un contesto, non puoi applicarvi una
normale funzione:

![no_fmap_ouch]({{ site.url }}/images/no_fmap_ouch.png)

Questo &grave; il momento per `fmap` di entrare in azione. `fmap` sa come
applicare funzioni a valori che si trovano all'interno di un contesto. Per esempio,
immagina di voler applicare `(+3)` a `Just 2`. Usa `fmap`:

```haskell
> fmap (+3) (Just 2)
Just 5
```
![fmap_apply]({{ site.url }}/images/fmap_apply.png)

**Bam!** `fmap` ci fa vedere come si fa! Ma come fa `fmap` a sapere come applicare
la funzione?

### Quindi cos'&egrave; un Functor?

`Functor` &egrave; una [typeclass](http://learnyouahaskell.com/types-and-typeclasses#typeclasses-101).
Ecco la definizione:

![functor_def]({{ site.url }}/images/functor_def.png)

*1. per rendere un data type f un functor*

*2. quel data type deve definire come `fmap` interagir&agrave; con lui*

Un `Functor` &egrave; un qualunque data type che definisce come `fmap` deve
essere applicato a s&grave; stesso. Ecco come funziona `fmap`:

![fmap_def]({{ site.url }}/images/fmap_def.png)

*1. `fmap` prende una funzione (come `(+3)`)*

*2. e un functor (come `Just 2`)*

*3. e ritorna un nuovo functor (come `Just 5`)*

Questo ci permette di fare:

```haskell
> fmap (+3) (Just 2)
Just 5
```

E `fmap` magicamente applica questa funzione, dato che `Maybe` e' un Functor.
Specifica come `fmap` si applica ai `Just` e ai `Nothing`.
 
 ```haskell
 instance Functor Maybe where
    fmap func (Just val) = Just (func val)
    fmap func Nothing = Nothing
```

Ecco quello che succede dietro le scene quando scriviamo `fmap (+3) (Just 2)`:

![fmap_just]({{ site.url }}/images/fmap_just.png)

*1. scarta il valore dal contesto*

*2. applica la funzione*

*3. reincarta il valore nel contesto*

Cosa succede se chiediamo a `fmap` di applicare `(+3)` a `Nothing`?

![fmap_nothing]({{ site.url }}/images/fmap_nothing.png)

*1. nessun valore*

*2. non applicare la funzione*

*3. il risultato &egrave; Nothing*

```haskell
> fmap (+3) Nothing
Nothing
```

Come Morpheus in Matrix, `fmap` semplicemente sa cosa fare; `Nothing` entra e
`Nothing` esce! `fmap` &egrave; zen. A questo punto l'esistenza del data type
`Maybe` ha un senso. Per esempio, ecco come lavoreresti con un record di un
database in un linguaggio che non ha `Maybe`:

```
post = Post.find_by_id(1)
if post
    return post.title
else
    return nil
end
```

Ma in Haskell:

```haskell
fmap (getPostTitle) (findPost 1)
```

Se `findPost` trova un post, otterremo il titolo con `getPostTitle`. Se ritorna
`Nothing`, il risultato finale sar&agrave; `Nothing`! Interessante, no?
`<$>` &grave; la versione *infix* di `fmap`, per cui spesso vedrai la versione
 equivalente:
 
 ```haskell
 getPostTitle <$> (findPost 1)
 ```
 
 Ecco un altro esempio: cosa succede quando applichi una funzione a una lista?
 
 ![fmap_list]({{ site.url }}/images/fmap_list.png)

 *1. un array di valori*
 
 *2. applica la funzione a ciascun valore*
 
 *3. un nuovo array di valori*
 
 Anche le liste sono Functor! Ecco la definizione:
 
 ```haskell
 instance Functor [] where
    fmap = map
```

Okay, un ultimo esempio: cosa succede quando applichi una funzione ad un'altra
funzione?

```haskell
fmap (+3) (+3)
```

Questa e' una funzione:

![function_with_value]({{ site.url }}/images/function_with_value.png)

Questa &egrave; una funzione applicata ad un'altra funzione:

![fmap_function]({{ site.url }}/images/fmap_function.png)

Il risultato &egrave; un'altra funzione!

```haskell
> import Control.Applicative
> let foo = fmap (+3) (+2)
> foo 10
15
```

Quindi anche le funzioni sono Functor!

```
instance Functor ((->) r) where
    fmap f g = f . g
```

Quando usi `fmap` su una funzione, semplicemente stai componendo due funzioni!

### Applicative

A breve...