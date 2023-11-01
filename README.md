# TP2 : Hadoop MapReduce

## Introduction
Ce project décrit deux exercices pratiques utilisant Hadoop MapReduce pour traiter des données structurées et non structurées. Les exercices couvrent la manipulation de données de vente et l'analyse de fichiers journaux Web.

## Exercice 1 : Traitement de données de vente
### Objectif
Développer un job Hadoop MapReduce pour calculer le total des ventes par ville à partir d'un fichier texte (ventes.txt) contenant les ventes d'une entreprise dans différentes villes. La structure du fichier ventes.txt est la suivante :
```
date ville produit prix
```
### Travail à faire
1. Développer un job MapReduce pour calculer le total des ventes par ville.
2. Développer un deuxième job pour calculer le prix total des ventes des produits par ville pour une année donnée.

### La tâche 1
1. Code source du Driver
```java
public class Driver {
    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);

        job.setJarByClass(Driver.class);
        job.setMapperClass(JobMapper.class);
        job.setReducerClass(JobReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(DoubleWritable.class);

        job.setInputFormatClass(TextInputFormat.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        job.waitForCompletion(true);
    }
}

```

2. Code source du Mapper
```java
public class JobMapper extends Mapper<LongWritable, Text,Text,DoubleWritable> {
    @Override
    protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, DoubleWritable>.Context context) throws IOException, InterruptedException {
        String[] tokens = value.toString().split(" ");
        if (tokens.length == 4) {
            String city = tokens[1];
            double price = Double.parseDouble(tokens[3]);
            context.write(new Text(city), new DoubleWritable(price));
        }
    }
}
```

3. Code source du Reducer
```java
public class JobReducer extends Reducer<Text, DoubleWritable,Text,DoubleWritable> {
    @Override
    protected void reduce(Text key, Iterable<DoubleWritable> values, Reducer<Text, DoubleWritable, Text, DoubleWritable>.Context context) throws IOException, InterruptedException {
        double totalSales = 0.0;
        for (DoubleWritable value : values) {
            totalSales += value.get();
        }
        context.write(key, new DoubleWritable(totalSales));
    }
}
```

4. Test avec une list des ventes

![List de test](assets/ex1j1_1.png)

5. Execution
```
hadoop jar TP-MAPREDUCE-1.0-SNAPSHOT.jar com.slimani_ce.exercice1.job1.Driver /ventes.txt /ex1_job1_output
```
![List de test](assets/ex1j1_2.png)

6. Résultats

![List de test](assets/ex1j1_3.png)

### La tâche 2
1. Code source du Driver
```java
public class Driver {
   public static void main(String[] args) throws Exception {
      Configuration conf = new Configuration();
      Job job = Job.getInstance(conf);

      job.setJarByClass(Driver.class);
      job.setMapperClass(JobMapper.class);
      job.setReducerClass(JobReducer.class);

      job.setOutputKeyClass(Text.class);
      job.setOutputValueClass(DoubleWritable.class);

      job.setInputFormatClass(TextInputFormat.class);

      FileInputFormat.addInputPath(job, new Path(args[0]));
      FileOutputFormat.setOutputPath(job, new Path(args[1]));

      job.waitForCompletion(true);
   }
}
```

2. Code source du Mapper
```java
public class JobMapper extends Mapper<LongWritable, Text, Text, DoubleWritable> {
   @Override
   protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, DoubleWritable>.Context context) throws IOException, InterruptedException {
      String[] tokens = value.toString().split(" ");
      if (tokens.length == 4) {
         String city = tokens[1];
         double price = Double.parseDouble(tokens[3]);
         context.write(new Text(city), new DoubleWritable(price));
      }
   }
}
```

3. Code source du Reducer
```java
public class JobReducer extends Reducer<Text, DoubleWritable, Text, DoubleWritable> {
   @Override
   protected void reduce(Text key, Iterable<DoubleWritable> values, Reducer<Text, DoubleWritable, Text, DoubleWritable>.Context context) throws IOException, InterruptedException {
      double totalSales = 0.0;
      for (DoubleWritable value : values) {
         totalSales += value.get();
      }
      context.write(key, new DoubleWritable(totalSales));
   }
}
```

4. Test avec une list des ventes

![List de test](assets/ex1j2_1.png)

5. Execution (Avec l'année 2023 comme paramètre)
```
hadoop jar TP-MAPREDUCE-1.0-SNAPSHOT.jar com.slimani_ce.exercice1.job2.Driver /ventes.txt /ex1_job2_output 2023
```
![List de test](assets/ex1j2_2.png)

6. Résultats

![List de test](assets/ex1j2_3.png)