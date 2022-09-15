

안녕하세요!

공부한 내용을 구현한 코드를 업로드하는 곳입니다.



+ Pyspark

| name                                                      | summary                                                      |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| [tutorial_01](./ssafy_second_pjt/spark/tutorial_01.ipynb) | Data load                                                    |
| [tutorial_02](./ssafy_second_pjt/spark/tutorial_02.ipynb) | Basic - select, drop, withColumn, withColumnRenamed          |
| [tutorial_03](./ssafy_second_pjt/spark/tutorial_03.ipynb) | Handling Missing Value                                       |
| [tutorial_04](./ssafy_second_pjt/spark/tutorial_04.ipynb) | filter()                                                     |
| [tutorial_05](./ssafy_second_pjt/spark/tutorial_05.ipynb) | groupBy & aggregate                                          |
| [tutorial_06](./ssafy_second_pjt/spark/tutorial_06.ipynb) | Examples of ML - RandomSplit, VectorAssembler, LinearRegression |
| [tutorial_08](./ssafy_second_pjt/spark/tutorial_08.ipynb) | LR with Pyspark - Categorical Features(StringIndexer)        |







## Spark

+ [sparkMLlib](https://spark.apache.org/examples.html)



### [1] Youtube Tutorial

**[tutorial_01](./ssafy_second_pjt/spark/tutorial_01.ipynb)** - **Dataload**

+  `spark.read.csv()`
+ `.head()`
+ `.printSchema()`
+ `.toPandas()`



**[tutorial_02](./ssafy_second_pjt/spark/tutorial_02.ipynb)** - **Basic**

+ `.columns`

+ `.select('*')` 

+  `.dtypes` 

+ `.describe()`

+ `.withColumn('new_column', col('기존')+2)` 

+  `.drop('col1','col2')`

+  `.withColumnRenamed('Name', 'New Name')`

  

**[tutorial_03](./ssafy_second_pjt/spark/tutorial_03.ipynb)** -  **Handling Missing Value**

+ `.dropna()`

  + `.dropna(how='all')` : 모든 column이 null 인 recored 삭제
  +  `.dropna(how='any',thresh=3)`  :  non-null 값 개수가 thresh 미만이면 삭제
  + `.dropna(how='any', subset=['col1'])`  : subset에 있는 column 삭제

+ `.fillna({'col1': '대체값1', 'col2': '대체값2' })`

+ `Imputer()` : 결측치를 mean, median, mode 로 대체

  + ```python
    # 생성
    from pyspark.ml.feature import Imputer
    
    imputer = Imputer(
        inputCols=['Age','Experience', 'Salary'],
        outputCols=['{}_imputed'.format(c) for c in ['Age','Experience', 'Salary']]
    ).setStrategy("mean")  # mean, median, mode
    ```

  + ```py
    # fit & transform
    imputer.fit(sdf).transform(sdf).show()
    ```

  




**[tutorial_04](./ssafy_second_pjt/spark/tutorial_04.ipynb)** - **filter()**

+ `.filter()`

  + 사용법

    1. `.filter("Salary<=35000").select(['col1','col2']).show()`

    2. `.filter(col("Salary") <=35000).select(['col1','col2']).show()`

    3. `.filter((sdf['Salary']<=35000) & (sdf['Salary']>=30000)).show()`

  +  &, |, ~사용법

    + `.filter((col("Salary") <=35000) & (col("Salary") >=30000)).show()`
    + `.filter(~(col("Salary") <=35000)).show()`



**[tutorial_05](./ssafy_second_pjt/spark/tutorial_05.ipynb)** - **groupBy & aggregate**

+ `.groupBy('col')`

  + `.sum().show()`
  + `.mean().show()`
  + `.count().show()`
  + `.max().show()`

+ `.agg()`

  + ```py
    from pyspark.sql.functions import col, max, min, avg, sum, count
    
    sdf.groupBy('Name').agg(max('Salary').alias('max salary'), min('Salary'), avg('Salary'), count('Salary')).show()
    ```

    



**[tutorial_06](./ssafy_second_pjt/spark/tutorial_06.ipynb)** - **Examples of ML**

+ `VectorAssembler(inputCols=[], outputCol='')`

  + ```python
    from pyspark.ml.feature import VectorAssembler
    assembler = VectorAssembler(inputCols=['Age','Experience'], outputCol='Features')
    output = assembler.transform(training)
    ```

+ `.randomSplit([0.7,0.3], seed=1)`

  + ```python
    ### Features가 설명변수, Salary가 종속변수
    final_data = output.select('Features', 'Salary')
    train, test = final_data.randomSplit([0.75,0.25], seed=1)
    ```

+ `LinearRegression(featuresCol='', labelCol='')`

  + ```python
    from pyspark.ml.regression import LinearRegression
    ### lr estimator & model
    lr = LinearRegression(featuresCol='Features', labelCol='Salary')
    lr_model = lr.fit(train)
    ```

  + ```python
    ### coef, intercept
    lr_model.coefficients, lr_model.intercept
    ```

  + ```python
    ### prediction1
    pred = lr_model.transform(test)
    
    ### prediction 2 : evaluate 이용해서 이렇게하면, pred2에서 통계량, error 등 여러 옵션 볼 수 있음
    pred2 = lr_model.evaluate(test)
    pred2.predictions.show()
    ```

    





**[tutorial_08](./ssafy_second_pjt/spark/tutorial_08.ipynb)** - **LR with Pyspark**

+ `StringIndexer()`

  + ```python
    ### 1개변환도 가능하고 여러개 변환도 가능
    from pyspark.ml.feature import StringIndexer
    indexer = StringIndexer(inputCol='sex', outputCol='sex_indexed')
    indexer = StringIndexer(inputCols=['sex','smoker','day','time'], outputCols=['sex_index','smoker_index','day_index','time_index'])
    ```

+ LR

  + ```python
    ### LR
    from pyspark.ml.regression import LinearRegression
    lr = LinearRegression(featuresCol='features', labelCol='total_bill')
    lr_model = lr.fit(train)
    pred = lr_model.evaluate(test)
    pred.predictions, pred.r2, pred.meanAbsoluteError, pred.meanSquaredError	
    ```

    



