# Scala


# Table of Contents

# Useful
### File exists
```scala
new java.io.File(file_path).isFile
```
### Split List given seperator
```scala
import scala.collection.mutable.ListBuffer  
  def listSplit[T](collection:Seq[T],seperator:T):Seq[Seq[T]]= {
    val name = ListBuffer(ListBuffer[T]())
    collection foreach {e =>
      if(e == seperator){
        name += ListBuffer[T]()
      } else {
        name.last += e
      }
    }
    name.map(_.toSeq).toSeq
  }
 ```
