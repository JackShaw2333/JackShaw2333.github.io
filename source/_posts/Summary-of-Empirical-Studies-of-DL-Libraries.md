---
title: Summary of Empirical Studies of DL Libraries
mathjax: true
date: 2022-02-16 16:38:33
tags:
---

## 0. Paper list

- Jia, L., Zhong, H., Wang, X., Huang, L., & Lu, X. (2020). **An Empirical Study on Bugs Inside TensorFlow**. In Lecture Notes in Computer Science (including subseries Lecture Notes in Artificial Intelligence and Lecture Notes in Bioinformatics): Vol. 12112 LNCS (pp. 604–620). https://doi.org/10.1007/978-3-030-59410-7_40
- Du, X., Xiao, G., & Sui, Y. (2020). **Fault triggers in the tensorflow framework: An experience report**. Proceedings - International Symposium on Software Reliability Engineering, ISSRE, 2020-October, 1–12. https://doi.org/10.1109/ISSRE5003.2020.00010
- Sun, X., Zhou, T., Wang, R., Duan, Y., Bo, L., & Chang, J. (2021). **Experience report: investigating bug fixes in machine learning frameworks/libraries**. Frontiers of Computer Science, 15(6), 156212. https://doi.org/10.1007/s11704-020-9441-1
- Tambon, F., Nikanjam, A., An, L., Khomh, F., & Antoniol, G. (2021). **Silent Bugs in Deep Learning Frameworks: An Empirical Study of Keras and TensorFlow**. http://arxiv.org/abs/2112.13314

<!--more-->

## 1. Conclusion

- In TensorFlow
  - Symptoms and root causes are more like ordinary projects
  - Major reported bugs are semantics bugs in **kernel**, tensor computation bugs in **API**
  - 78.17% bugs are *Bohrbug*
  - 16.47% are *Mandelbug*
- In ML libraries (Scikit-learn, Paddle, Caffe, SciPy, TensorFlow)
  - Main bug types
    - variable bug, 24.28%
    - design defect, 22.26%
    - compatibility bug, 21.51%
  - Maintenance types
    - corrective, 45.90%
    - adaptive, 20.77%
    - perfective, 31.63%