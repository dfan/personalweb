+++
date = "2021-06-20"
title = "Paper Summary for ShotCoL: Self-Supervised Video Representation Learning for Scene Boundary Detection in Movies and TV Episodes"
math = "true"
+++

*15 minute read.*  
*Disclaimer: this paper summary exclusively represents my own views and DOES NOT represent the views of my employer nor my institution. This post should not in any way be treated as an official reference.*

Roughly a year ago, I started doing research in self-supervised video representation learning with a focus on understanding long-form content. That work culminated in a second-author [CVPR 2021 paper](https://arxiv.org/abs/2104.13537), which is the focus of this post. This post is a purely informal paper summary that I challenged myself to write in order to improve my writing and communication skills. I will start by motivating the problem, and then explain the intuition behind our method and how it differs from both previous image and video-based self-supervised learning approaches. Finally, I will conclude by summarizing the key results in layman terms.

## Motivation
Labeling data at scale is expensive and time-consuming. In general, the amount of unlabeled data in the world far exceeds the amount of labeled data and new data is created everyday. To give a concrete example, suppose that you were asked to label all movies in existence for the task of recognizing actions. This would likely take dozens of years even if you divided this work among many friends and continuously labeled movies without sleeping nor eating. This is just for one task, so what if you wanted to then obtain labels for another task?

This is a big obstacle for supervised machine learning, as current state-of-the-art models require large amounts of labeled data in order to perform well, yet many machine learning tasks do not have sufficient labeled data. In contrast, self-supervised learning seeks to leverage invariances inherently present in unlabeled data, and learn a representation that can be used for a wide range of downstream tasks. This makes self-supervised learning especially promising in a limited-label setting where large amounts of unlabeled data would otherwise go unused.

My CVPR 2021 paper presents **ShotCoL,** which is a new state-of-the-art contrastive learning algorithm for scene boundary detection. Using shots from movies, ShotCoL teaches a model to represent visual and audio inputs by pulling together a given sample and its nearest neighbor in the embedding space, and pushing apart randomly selected shots from further away. ShotCoL is different from and substantially more effective than other works which teach a model to be invariant between two augmented versions of the same image, such as MoCo *[1]* and SimCLR *[2]*.
 
Next, I will define the problem of scene boundary detection.

![](images/blog/cvpr2021/ShotCoL_Diagram.png)  
*An overview of the ShotCoL method. The model learns to pull together a given shot and its nearest neighboring shot in the embedding space, and push apart randomly selected shots. Any model can be plugged into the ShotCoL framework and any modality can be used (e.g. visual, audio, text).*
 
## Scene Boundary Detection - A Fundamental Capability for Understanding Movies and TV Episodes
In video production, a **“shot”** is defined as a series of frames captured from the same camera over an uninterrupted period of time, while a **“scene”** is defined as a series of shots depicting a semantically cohesive part of a story. Although shot localization can be accurately achieved with low-level visual cues, scene localization is difficult and requires higher-level semantic understanding of the video. Thus, scene boundary detection is an important step towards building semantic understanding of movies and TV episodes, with numerous industrial applications. Scene boundary detection can be formulated as a binary classification problem of predicting whether a given shot boundary is also a scene boundary.

![](images/blog/cvpr2021/shot_scene.png)  
*Two scenes from Stuart Little are depicted here. A scene is composed of multiple shots. Determining where a scene begins and ends requires high-level understanding of the video.*

Next, I will explain our approach at a high-level and give intuition for why it is effective for video representation learning.

## Self-Supervised Learning
Self-supervised learning is a subset of learning methods that does not use human-annotated labels, and typically involves solving a pretext or surrogate task. The purpose of the pretext task is to allow the model to learn representations of unlabeled data using pseudo-labels generated automatically from the data itself. The resultant learned representation can be used for many different downstream tasks.

Our work draws inspiration from self-supervised contrastive learning - a subset of self-supervised learning methods that defines the pretext task as pulling positive pairs together while pushing apart negative pairs. There are many plausible ways of defining a positive pair for this pretext task, so let’s consider a few and then present our own novel pretext task.

### Shot-Based Invariance for Representation Learning
Previous works in contrastive learning such as MoCo *[1]* and SimCLR *[2]* focus on image classification, and learn a representation in a self-supervised manner by defining positive pairs using augmented versions of the same image. The idea is that augmented versions of the same image should be similar in the embedding space, so the model should be able to maximize similarity between an image and its augmented version, while minimizing similarity with other randomly selected images.

In contrast, the core intuition behind ShotCoL is that nearby shots are more likely to be similar to each other than shots randomly selected from further away. This intuition holds in practice due to the coherent storyline that is communicated by shots within a scene - nearby shots tend to have the same set of actors enacting a semantically cohesive story, and are therefore in expectation more similar to each other than a set of randomly selected shots.

Thus, for a given query shot, ShotCoL defines **its positive key as the closest neighboring shot** within the embedding space, and all other non-neighboring shots as negatives. By leveraging this invariance, our pretext task is able to implicitly capture the shot-based structure of movies which defines semantic progression. Note that naive approaches (such as selecting a shot that is a fixed number of steps before or after the query shot) do not always capture a relevant relationship to the query shot. Different possible ways of selecting a positive pair are depicted below.

![](images/blog/cvpr2021/augmentation.jpg)  

*Different methods of defining a positive key to a query shot are shown. The center three approaches do not adequately capture temporal relations within the movie. In contrast to augmentation based works, ShotCoL explicitly encodes the temporal structure of videos into our pretext task for representation learning. For a given shot, its positive key is defined as the closest shot in the embedding space within a local neighborhood.*

By using shots as the basic unit instead of individual frames, ShotCoL is also able to efficiently condense movies into a compact space and reduce redundancy, thus providing one answer to the open research question of how to efficiently represent videos for deep learning. 

Next, I will present key results on the MovieNet dataset *[3]*, which is a publicly available dataset with rich annotations for $1,100$ movies, $318$ of which have scene annotations.


## Key Results

### Effectiveness of our Representation for Shot Retrieval
Intuitively, a good representation for scene boundary detection should be able to project shots from within the same scene to be close to each other in the embedding space. To evaluate this, we projected all shots from the MovieNet test set into features, and then for each shot’s feature, computed its five closest neighbors via cosine similarity on the projected features. The below figure shows qualitatively what the five nearest neighbor shots look like when you use three different feature spaces.

![](images/blog/cvpr2021/qualitiative_representation_quality.png)
*Green text denotes that the retrieved shot belongs to the same scene as the query shot and red text denotes otherwise.*

In this example, all five closest shots retrieved by ShotCoL belong to the same scene. However, when using features precomputed from popular pre-trained models such as those trained on ImageNet *[4]*, this is not the case. This highlights the effectiveness of our representation at clustering shots from within the same scene to be close together in the embedding space.


### Effectiveness of ShotCoL’s Pretext Task
Next, let’s distill the effectiveness of our nearest-neighboring shot based pretext task using results from the MovieNet test set. Let's first compare against pre-trained feature spaces and previous state-of-the-art, and then look at self-supervised approaches.

**Compared to Supervised Approaches:**  
Let us first consider popular pre-trained feature spaces such as ImageNet and Places as a baseline. When pre-trained ImageNet and Places features are directly used for scene boundary detection, the Average Precision (AP) is $41.26$ and $43.23$ respectively. Compared to ImageNet, ShotCoL improves the AP by over $12$ points absolute ($29.3\%$ relative).

Next, let’s compare to LGSS *[5]* which was the previous state-of-the-art method for scene boundary detection. Compared to LGSS, ShotCoL improves the AP by over $6$ points absolute ($13.3\%$ relative), showing that ShotCoL can learn a more effective representation for scene boundary detection than previous supervised approaches. This further supports the argument that self-supervised learning has high potential for closing the gap between fully-supervised approaches, or even surpassing supervised approaches in the long-run.

![](images/blog/cvpr2021/supervised_movienet_comparison_new.png)  
*ShotCoL achieves new state-of-the-art AP ($+13.3\%$ relative) while using 9x fewer parameters and running $7x$ faster, and only using a single modality compared to four.*

**Compared to Self-Supervised Approaches:**  
There are two questions to consider when comparing against self-supervised approaches. First, how does our nearest-neighbor based pretext task compare to the image augmentation based pretext task popularized by MoCo and SimCLR? Second, is our method agnostic to the choice of learning framework?

After training ResNet50 using the image augmentation based pretext task in MoCo and SimCLR on unlabeled shots from MovieNet, we obtained an AP of $42.51$ and $41.65$ respectively, which is nearly identical to the results obtained from ImageNet pre-trained features. This indicates that the image augmentation based pretext task fails to capture any meaningful information from videos, which (unlike images) carry a temporal aspect.

After swapping the image augmentation based pretext task with ShotCoL’s shot similarity based pretext task, the AP improves from $41.65$ to $50.45$ ($+21.1\%$ relative) for SimCLR, and $42.51$ to $53.37$ ($+25.5\%$ relative) for MoCo. This demonstrates that our pretext task is especially effective for learning a useful representation for movie understanding, and is agnostic to the specific learning framework used. 

![](images/blog/cvpr2021/selfsupervised_movienet_comparison_new.png)  
*Our pretext task consistently improves performance regardless of the self-supervised learning framework it is inserted in. For both SimCLR and MoCo, there is a clear improvement over the image augmentation based pretext task.*


### Label Efficiency
When training a classifier on the embedding learned from ShotCoL for the downstream task of scene boundary detection, we can achieve equivalent performance to previous state-of-the-art by using only $~25\%$ of the training set labels in MovieNet. Furthermore, we can achieve equivalent performance to popular pre-trained embeddings such as ImageNet by using only $~10\%$ of the training labels. By learning a rich representation from unlabeled data, fewer labels are required to train a classifier for the downstream task compared to a fully-supervised approach, further illustrating the efficacy of self-supervised learning at making use of unlabeled data.

![](images/blog/cvpr2021/movienet_labelefficiency.png)  
*Compared to previous state-of-the-art [5] on MovieNet, only $~25\%$ of training labels are required to achieve equivalent performance using ShotCoL. Additionally, ShotCoL can achieve equivalent performance as ImageNet pre-trained features using just only $~10\%$ of the labels. This illustrates that self-supervised learning can lead to greater label efficiency in downstream tasks.*

### Cross-Dataset Generalization
Cross-dataset generalization remains an open problem in computer vision; a model trained for one task or dataset may not perform well in a different setting. In contrast, self-supervised approaches are promising for learning embeddings that generalize well across unseen data, and this has already been observed in the literature. For example, in the original MoCo paper, MoCo embeddings outperform their supervised pre-training counterparts in several different object segmentation and detection benchmarks, despite only being trained on unlabeled ImageNet data.

To evaluate cross-dataset generalization, we freeze the weights of ShotCoL to extract features, and then train a simple classifier on top of these extracted features. We then consider performance on AdCuepoints, which is a dataset for a **different task** of advertisement cue-point insertion. If ShotCoL has learned a generalized representation, then it should perform well on both advertisement cue-point insertion and scene boundary detection regardless of what unlabeled data the model was first trained on.

When ShotCoL is pre-trained on unlabeled MovieNet, it is able to achieve nearly equivalent performance on the AdCuepoints test set, as when ShotCoL was pre-trained on unlabeled AdCuepoints. The same also holds true when ShotCoL is pre-trained on unlabeled AdCuepoints and evaluated on the MovieNet test set. This indicates that self-supervised learning has good potential for learning generalizable features, even when the model is only trained on a single pretext task that is not directly related to the downstream tasks.

I personally believe that further developments in self-supervised learning will reduce the amount of effort required to develop models in the future, since one will no longer need to train a highly specialized model for every single task in order to achieve state-of-the-art performance. In recent literature, other researchers have also observed similar properties for other self-supervised models.

![](images/blog/cvpr2021/ShotCoL_TransferLearning.png)
 

###  Multimodal Learning and Temporal Modeling
ShotCoL allows for independent usage of additional modalities such as raw audio. When ShotCoL is trained on raw audio from the AdCuepoints dataset, we obtain a relative improvement in AP of $12.7\%$ compared to pre-trained AudioSet features from the PANN network *[6]*. This shows that our method is agnostic to the choice of input modality, further illustrating the potential of ShotCoL’s novel pretext task. The results also suggest that we could have further improved the performance on MovieNet if we had access to its raw audio.

![](images/blog/cvpr2021/Adcuepoints_SingleModal_Results.png)


Combining the visual and audio features learned by ShotCoL yields the highest results on AdCuepoints, indicating the importance of leveraging all available modalities for video understanding. The combined audio and visual features lead to a higher AP of $57.65$ compared to $53.98$ for the visual modality, when a MLP is used as the classifier. We also experimented with using a bidirectional LSTM and Transformer to do temporal modeling on the extracted features, leading to a higher AP of $59.02$ and $59.95$ respectively on AdCuepoints. Transformers are naturally equipped to model sequential data (unlike CNNs) and video data is naturally sequential. Recent advances such as sparse self-attention *[7]* make Transformers more computationally feasible than ever, and I anticipate further adoption of architectures from natural language processing to computer vision in the future.

![](images/blog/cvpr2021/AdCuepoints_Multimodal_Results.png) 

## What’s Next?
ShotCoL is an important step towards understanding temporal progression and semantic content within movies and TV episodes, however scene boundary detection is just one problem out of many. I hope that the insights provided by our work will lead to further advances in long-form video representation learning, and benefit other tasks which require higher level understanding of the content, such as action localization, movie question answering, and search and retrieval.

I feel very privileged to have had the opportunity to do this work, and I aspire to continue pushing the envelope of research in multi-modal video understanding. For more details on this particular work, please see our [CVPR 2021 paper](https://arxiv.org/abs/2104.13537). Stay tuned for my next post where I will talk about my journey of moving into industry research!

**Blog Post References**

1. Kaiming He, Haoqi Fan, Yuxin Wu, Saining Xie, and Ross Girshick. Momentum contrast for unsupervised visual representation learning. In *IEEE/CVF Conference on Computer Vision and Pattern Recognition*, 2020.

2. Ting Chen, Simon Kornblith, Mohammad Norouzi, and Geoffrey Hinton. A simple framework for contrastive learning of visual representations. In *Proceedings of the 37th International Conference on Machine Learning*, 2020.

3. Qingqiu Huang, Yu Xiong, Anyi Rao, Jiaze Wang, and Dahua Lin. Movienet: A holistic dataset for movie un- derstanding. In *European Conference on Computer Vision*, 2020.

4. Jia Deng, Wei Dong, Richard Socher, Li-Jia Li, Kai Li, and Li Fei-Fei. Imagenet: A large-scale hierarchical image database. In *Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition*, 2009. 

5. Anyi Rao, Linning Xu, Yu Xiong, Guodong Xu, Qingqiu Huang, Bolei Zhou, and Dahua Lin. A local-to-global approach to multi-modal movie scene segmentation. In *IEEE/CVF Conference on Computer Vision and Pattern Recognition*, 2020.

6. Qiuqiang Kong, Yin Cao, Turab Iqbal, Yuxuan Wang, Wenwu Wang, and Mark D. Plumbley. Panns: Large-scale pretrained audio neural networks for audio pattern recognition. *arXiv preprint arXiv:1912.10211,* 2020.

7. Sinong Wang, Belinda Li, Madian Khabsa, Han Fang, and Hao Ma. Linformer: Self-attention with linear complexity. *arXiv preprint arXiv:2006.04768*, 2020.