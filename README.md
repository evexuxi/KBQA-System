# KBQA-System
I was involved in the development of a closed-domain Q&amp;A system for a website with my partners, and I'm sharing it here in order to document the experience.

Because development time available for the project was rather short, the solution I provided can only be applied to closed-domain Q&A datasets, and at the same time, there may still be some problems that need to be improved. Suggestions and criticisms are welcome if any of these are inappropriate. Perhaps in 2024, I will go back and do a careful refinement of the functionality and performance enhancements. Thanks a lot!

Aiming at the widely demanded and diversified Q&A tasks in practical application scenarios, this project carries out research on the development of semantic Q&A system based on big models. The goal of this research is to develop an intelligent Q&A system with high efficiency and accuracy and good user experience, and to realise Q&A functions with high migrability and excellent system performance through in-depth research on key technologies such as semantic vector parsing, knowledge base design and retrieval, and front-end and back-end interaction.:sparkles::sparkles::zap::zap::construction::construction:

To summarise, this project is divided into four key parts.

1.By studying and applying the **SBERT** method for semantic vector parsing, accurate understanding and semantic matching of user input questions are achieved to provide high-quality question parsing.

2.Designing a knowledge base based on **MySQL** to organise and store relevant data effectively to support the Q&A function in the system. At the same time, **Milvus** is used to achieve semantic similarity calculation matching between user question feature vectors and knowledge base question vectors based on cosine similarity to improve the retrieval efficiency and accuracy of the knowledge base.

3.The **front-end and back-end** interactions are completed using **FastAPI**, which provides functions such as processing system logic, data storage, etc. in the back-end and communicates with the front-end through defined API interfaces, thus enabling different organisations to conveniently deploy and use this system.

4.Finally, we need to pay attention to the system's extensibility and maintainability in the light of the relevant development trends and requirements, and will subsequently add new business functions such as process-based guidelines in the light of the needs of different application scenarios, as well as further develop the search of large model Q&A in the **open domain**, so that the model approach can be quickly adapted and extended when business requirements change.   **TO BE CONTINUE**

