# Functors 

## Covariant Functors
```
class Functor f where 
  fmap :: (a -> b) -> f a -> f b
```

* The normal functor is actually a covariant functor whereby the fmap preserves the direction of the arrows 

```
      g ::   a ->   b
fmap  g :: f a -> f b
```

## Contravariant Functors

* In comparison, contravariant functors have the arrows in the opposite direction

```
class Contravariant f where 
  contramap :: (b -> a) -> f a -> f b
```

* *** the key difference is that contramap swaps the direction of the arrow ***

```
           g ::   a <-   b
contramap  g :: f a -> f b
```

## Bifunctors

* A bifunctor is a functor of two arguments

A kind type of the following: 
```
* -> * -> *
```

* Some examples of bifunctors are `Either`, `(,)` or `(->)`

```
class Bifunctor f where 
  bimap :: (a -> c) -> (b -> d) -> f a b -> f c d
```

```
      g   ::   a   ->   c
        h ::     b ->     d 
bimap g h :: f a b -> f c d   
```

## ProFunctors 

* A Profunctor is a bifunctor that is contravariant in the first argument and covariant in the second

```
class Profunctor f where 
  dimap :: (c -> a) -> (b -> d) -> f a b -> f c d
```

```
      g   ::   a   <-   c
        h ::     b ->     d 
bimap g h :: f a b -> f c d   
```


## Resources
* https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/profunctors