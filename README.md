# Q&A-SYSTEM BASED MILVUS AND SBERT

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/e345c483-b235-4a08-9f60-ce4be8889bee)

I was involved in the development of a closed-domain Q&amp;A system for a website with my partners, and I'm sharing it here in order to document the experience.

In addition, since the developers had no relevant experience at the beginning, this programme refered to and learned from the methods of many predecessors, among which, this one is one of the most important sources:https://github.com/milvus-io/bootcamp/tree/master/solutions/
:sparkles::sparkles::sparkles:Because time for the development of the project was rather short, the solution I provided can only be applied to closed-domain Q&A datasets, and at the same time, there may still be some problems that need to be improved. Suggestions and criticisms are welcome if any of these are inappropriate. Perhaps in 2024, I will go back and do a careful refinement of the functionality and performance enhancements. Thanks a lot!

Aiming at the widely demanded and diversified Q&A tasks in practical application scenarios, this project carries out research on the development of semantic Q&A system based on big models. The goal of this research is to develop an intelligent Q&A system with high efficiency and accuracy and good user experience, and to realise Q&A functions with high migrability and excellent system performance through in-depth research on key technologies such as semantic vector parsing, knowledge base design and retrieval, and front-end and back-end interaction.:sparkles::sparkles::zap::zap::construction::construction:

# :whale::whale:Preceding Content

The process can be found in this tutorial.

## 1. Start Milvus and MySQL

Install Milvus directly based on docker--->https://milvus.io/docs/install_standalone-docker.md

*Start Milvus v2.2*
```
$ wget https://github.com/milvus-io/milvus/releases/download/v2.2.10/milvus-standalone-docker-compose.yml -O docker-compose.yml 
$ sudo docker-compose up -d 
```
*Start MySQL*
```
$ docker run -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```
Subsequently, the project services can be run via Docker. Before that, let's take a look at the core of what exactly is involved.
# :memo::memo:Main Parts
To summarise, this project is divided into four key parts.

## 1:rocket::rocket:SBERT Method
By studying and applying the **SBERT** method for semantic vector parsing, accurate understanding and semantic matching of user input questions are achieved to provide high-quality question parsing.

Deployment of **paraphrase-multilingual-mpnet-base-v2** pre-training model for SBERT method：

First install the sentence-transformers library. Use the paraphrase-multilingual-mpnet-base-v2 model to get the embedding vectors of the sentences. The code will encode the input list of sentences and output the corresponding embedding vectors. The sample code is shown below:

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/33d8cd39-dd66-4aa5-bdf8-77f5e0e97f84)

## 2.:rocket::rocket:Milvus Library and MySQL
Designing a knowledge base based on **MySQL** to organise and store relevant data effectively to support the Q&A function in the system. At the same time, **Milvus** is used to achieve semantic similarity calculation matching between user question feature vectors and knowledge base question vectors based on cosine similarity to improve the retrieval efficiency and accuracy of the knowledge base.

Using the SBERT model the questions are converted into feature vectors and stored in Milvus, while Milvus will assign a vector id to these feature vectors. these ids representing the questions and their corresponding answers are stored in MySQL. The feature vectors obtained after processing the data are normalised and imported into Milvus for storage, and then the id returned by Milvus and the answer to the question corresponding to that id are imported into MySQL. The user inputs a question, and after generating a feature vector by SBert, the most similar one to it is found in the Milvus library. The cosine distance is used to represent the similarity between two sentences, and since all vectors are normalised, the closer the cosine distance between two feature vectors is to 1 means that the similarity is also higher.

Firstly class definition, declare a class named MilvusHelper to encapsulate the interactive operations with Milvus database, the main functions are:

①:construction:**Connection function**: first try to connect to the Milvus database and print debugging information when the connection is successful. If the connection fails, record the error message and exit the programme.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/213d49d5-5bdc-412f-87e0-89d1dd3a9a8b)

②:construction:**Insert function**: define insert method for bulk insertion of vector data into Milvus collection. First call the create_collection method to create the collection (if it doesn't exist), then insert the data into the collection and return the ID of the inserted vectors.

![be56a3ed3d3fa1650378c16aa83bd5e](https://github.com/evexuxi/KBQA-System/assets/122734805/3b7593ab-c67d-49f4-b915-b952a804778e)

③:construction:**Search_vectors function**: define the search_vectors function to search for vectors in the Milvus collection. It is called with the given vectors and search parameters.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/a92789ad-dccb-4e2e-be62-7f8c82a43df4)

④:construction:**Count function**: define the count function for getting the number of vectors in a Milvus collection. Calls the collection.num_entities property and returns the number of vectors.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/4b879835-4fd7-458d-bdfb-a3a988ed63cf)


## 3.:rocket::rocket:FastAPI Framework
The **front-end and back-end** interactions are completed using **FastAPI**, which provides functions such as processing system logic, data storage, etc. in the back-end and communicates with the front-end through defined API interfaces, thus enabling different organisations to conveniently deploy and use this system.

①:construction:**Introducing key components**

*from fastapi import FastAPI, File, UploadFile*      introduces the FastAPI framework and its related components for building Web APIs.
*from milvus_helpers import MilvusHelper*            introduces the MilvusHelper module, which is used to interact with Milvus.
*from mysql_helpers import MySQLHelper*              introduces the MySQLHelper module for interacting with MySQL.

②:construction:**Creating FastAPI applications**

With the following configuration, the FastAPI application instance app gains the ability to handle routing and cross-domain requests. Continue defining routes and other middleware on this instance to build the Web API.
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/ca232868-7f58-43c9-9cce-e35e210337d4)

③:construction:**Defining models, Milvus and MySQL examples**

By creating these instances and setting them as global variables, they can be used in various parts of the application in order to load the model, connect to the database and perform the appropriate operations, enabling the sharing and reuse of the model and database.
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/21683fc3-956f-4638-8d79-f3310a471cec)

④:construction:**Defining the API interface**

Define relevant interfaces.

 Interface Name | Function  | Input | Retern  
 ---- | ----- | ------ | ------- 
@app.post('/qa/load_data') | Load the standard question and answer bank | Q&A on csv files, database names |   \
@app.get('/qa/search') | Search for similar issues | Questions to be queried, database name  |  Successful or not, list of similar problems   
@app.get('/qa/get_answer') | Searching for answers to questions | Questions to be queried, database name  |  Successful or not, standard answers to the most similar questions
@app.get('/qa/get_pic') | Get answers in the form of pictures | Questions to be queried, database name  |  Successful or not, related images for this issue
@app.post('/qa/count') | Query the number of standardised quiz pairs in the current database | Database name   |  Successful or not, number of standard question-and-answer pairs
@app.post('/qa/drop') | Clear the current database of standard question and answer pairs | Database name   |  \

In the end, the results are as follows：
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/e4a00439-96fd-4f6b-ad12-9e3fec7132a2)

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/8869566e-dd68-4ccd-9e89-6812a24f470f)

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/d7a8752d-815d-44bd-afb6-65f3850c4b80)

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/44ef0327-6b99-4760-9292-e057e8903448)

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/6842d89c-66e9-49cf-898c-8c7cc1cf4323)


## 4.:rocket::rocket:System Experience & Testing
### Functional Testing
After completing the deployment of the model and the design of the front-end page, we have tested the intelligent question and answer system many times, and we have been improving on the basis of the previous code, trying to be able to complete more functions in the goal, the following is our testing process.
First of all, run the program and open the web page, we can see a concise dialogue box, users need to enter their questions in the text box below and click send.

Finally, we need to pay attention to the system's extensibility and maintainability in the light of the relevant development trends and requirements, and will subsequently add new business functions such as process-based guidelines in the light of the needs of different application scenarios, as well as further develop the search of large model Q&A in the **open domain**, so that the model approach can be quickly adapted and extended when business requirements change.   

The specific core elements are as follows：

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/8e394a08-7584-4e01-bd7e-373d166828df)

Now you can see in the dialogue box, intelligent customer service Q&A system has been based on the user's question to output the corresponding answer, which is the most core part of our Q&A system.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/1f661386-87d1-4fe8-9378-813d0e0c4122)

In addition, we have also returned three questions below the answer to the question that the user is likely to be interested in, the user only needs to use the cursor to select the question that needs to be consulted, the intelligent customer service Q&A system will answer the question selected by the user, and the answer will be output in the dialogue box.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/5a4de03a-5a06-4f44-8a49-e7dd83e2838c)

### Dataset Testing

In this test set contains 560 such questions, after testing can be found that the accuracy of the intelligent customer service Q&A system output answers can be close to 90%, the total time consumed is about 40s, which meets our expectations.

![image](https://github.com/evexuxi/KBQA-System/assets/122734805/faaabead-a1be-4126-8e4a-92a69a5d2f9b)

# :wrench::wrench:System Operation
Let's move on back to how you can run this Q&A system.

Upload the packed scr folder to the server and extract it.
```
$	cd scr
$	conda creat –name qa_sys python=3.7
$	conda activate qa_sys
```
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/ea03f02a-fe34-4aa1-a771-9fb5b720ee43)

After starting Milvus and MySQL as said before, start the server using Docker.
## Setup parameters
Please modify the parameters according to your own environment, here is a list of some parameters that need to be set.

 Parameter | Description  | Default setting  
 ---- | ----- | ------ 
MILVUS_HOST | The IP address of Milvus, you can get it by ifconfig | 127.0.0.1
MILVUS_PORT | Port of Milvus |  19530
VECTOR_DIMENSION| Dimension of the vectors | 384
MYSQL_HOST | The IP address of Mysql | 127.0.0.1
MYSQL_PORT | Port of Milvus | 3306

Please use your IP address to replace "127.0.0.1".

```
$ export MILVUS_HOST='<your-ip>'
$ export MILVUS_PORT='19530'
$ export MYSQL_HOST='<your-ip>'
```
## Run Docker
This image qa-chatbot-server:v1 is based on Milvus2.0-rc3, if you want to start Milvus2.0-rc5's quiz server using docker, build a new qa-chatbot image using Dockerfile.
```
$ docker run -d \
-p 8000:8000 \
-e "MILVUS_HOST=${MILVUS_HOST}" \
-e "MILVUS_PORT=${MILVUS_PORT}" \
-e "MYSQL_HOST=${MYSQL_HOST}" \
milvusbootcamp/qa-chatbot-server:2.2.10
```
## Run code
Then start the server using Fastapi.
```
$ python src/remain.py
```
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/be6ab92b-3e66-44b0-a1e3-b92dd6f39290)
## intereaction
Open in your browser: http://localhost:4000/
Type your question in the input box to ask and click send.
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/e96eb8b1-5ad5-46cf-a3c0-a55453f41f3d)

You can get the answer, and it will return similar questions as an alternative, if necessary, you can directly click on the question you want to ask, it will further return the answer.
![image](https://github.com/evexuxi/KBQA-System/assets/122734805/8b1bf599-f104-4f87-abe0-3a0a00ad1610)
