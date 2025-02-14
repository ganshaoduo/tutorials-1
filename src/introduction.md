# Introduction

Bagua is a distributed training utility developed by Kuaishou Technology and DS3 Lab. It aims to provide a system abstraction that is both flexible and performant to support state-of-the-art system relaxation techniques of distributed training. Powered by the new system design, Bagua has a great ability to implement and extend various state-of-the-art distributed learning algorithms. Researchers can easily develop new distributed training algorithms based on bagua, *without sacrificing system performance*.

Currently bagua has integrated primitives like *Centralized Synchronous Communication (AllReduce)*, *Decentralized Synchronous Communication*, *Low Precision Communication* and many more. Its effectiveness has been verified in various scenarios, including VGG-16 and ResNet-50 on ImageNet, Bert Large on SQuAD and many industrial applications at Kuaishou. 

# Links

* [Bagua Github](https://github.com/BaguaSys/bagua)
* [Bagua Tutorials](https://baguasys.github.io/tutorials)
* [Bagua API Documentation](https://bagua.readthedocs.io/)
