[Serialization](http://en.wikipedia.org/wiki/Serialization) is the process of saving an object in a persistent form, e.g., on the harddisk as a bytestream. **Deserialization** is the process in the opposite direction, creating an object from a persistently saved data structure.
In [Java](http://en.wikipedia.org/wiki/Java_programming_language), an object can be serialized if it imports the [java.io.Serializable](https://docs.oracle.com/javase/8/docs/api/java/io/Serializable.html) interface. 

Members of an object that are not supposed to be serialized, need to be prefixed with the keyword **transient**.

In the following you'll find some Java code snippets for serializing and deserializing a `J48` classifier. Of course, serialization is not limited to classifiers. Most schemes in Weka, like clusterers and filters, are also serializable.

# Serializing 
Here we create a J48 classifier `cls`, train it with a dataset `/some/where/data.arff`, and save the built model to a file `/some/where/j48.model`.

```java
 // create J48
 Classifier cls = new J48();
 
 // train
 Instances inst = new Instances(
                    new BufferedReader(
                      new FileReader("/some/where/data.arff")));
 inst.setClassIndex(inst.numAttributes() - 1);
 cls.buildClassifier(inst);
 
 // serialize model
 ObjectOutputStream oos = new ObjectOutputStream(
                            new FileOutputStream("/some/where/j48.model"));
 oos.writeObject(cls);
 oos.flush();
 oos.close();
```

If you use the `SerializationHelper` class, then this shrinks to:

```java
 // serialize model
 weka.core.SerializationHelper.write("/some/where/j48.model", cls);
```

# Deserializing
Here the previously saved model is deserialized as `cls` and again available for classification.

```java
 // deserialize model
 ObjectInputStream ois = new ObjectInputStream(
                           new FileInputStream("/some/where/j48.model"));
 Classifier cls = (Classifier) ois.readObject();
 ois.close();
```

Or, with the `SerializationHelper` class:

```java
 // deserialize model
 Classifier cls = (Classifier) weka.core.SerializationHelper.read("/some/where/j48.model");
```

# Serialization in Weka
The Explorer serializes the classifier and the training header together. This makes it easy to test whether a dataset is compatible with the dataset the classifier was trained with. The commandline option **-d <file>** of the *developer version* stores the training header as well.

In order to read serialized models that contain the header information as well, you can use the `readAll` method of the `weka.core.SerializationHelper`. For serializing models with their datasets, use `writeAll`.

# See also
* [Use Weka in your Java code](use_weka_in_your_java_code.md)

# Links
* [Java Serialization Specifications](https://docs.oracle.com/javase/8/docs/platform/serialization/spec/serialTOC.html)

