# Haskell


Structure of a main script with let and do. Save in a script e.g. example.hs

```haskell
main :: IO ()
main = do
  let
    printString = "Hi"
    number = 3
  putStrLn printString
  (putStrLn . show) number


```
To compile and run we use `stack exec -- runghc examplehs
