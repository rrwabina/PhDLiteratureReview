## Contrastive Learning + Federated Learning

## [Summary]
### Interesting methods 
- Reference [2]: Domain Specific Batch Normalization - individually normalize feature maps for the two anatomical domains and use a cross-domain contrastive learning strategy to extract domain invariant features. [https://github.com/wgchang/DSBN]
- Reference [2]: Psuedo-label methods for SSL can be improved using: (a) random propagation, (b) uncertainty-based refinement, (c) adversarial learning-based methods, (d) discriminators, (e) perturbation-based learning
- Reference [2]: Pseudo-label methods for SSL can be improved using mean teacher (i.e., uses a self-ensembling of a student model as the teacher model, and encourages consistent predictions between the two models). It has also been extended to **Uncertainty-Aware Mean Teacher**.


## [1] Federated Contrastive Learning for Volumetric Medical Image Segmentation
<code>
@inproceedings{wu2021federated,
  title={Federated contrastive learning for volumetric medical image segmentation},
  author={Wu, Yawen and Zeng, Dewen and Wang, Zhepeng and Shi, Yiyu and Hu, Jingtong},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={367--377},
  year={2021},
  organization={Springer}
}
</code>

### Problem Statement
- Existing Federated Learning models are highly limited across clients because data sharing are private and illegal. Combining a large dataset consisting of very sensitive and private medical data in a single location is impractival as well.
### Clinical Motivations
- Medical image datasets exist in isolated medical centers and hospitals. Combining a large dataset consisting of very sensitive and private medical data in a single location is impractical and illegal. 
- Federated learning is an effective approach in which distributed clients collaboratively learn a shared model while keeping private raw data local. 

### Technical Motivations
- Existing FL approaches use supervised learning on each client (i.e., computers) and require that all data are labeled. However, annotating all the medical images is usually unrealistic due to high labeling cost and requirement of expertise. **The deficiency of labels makes supervised FL impractical.**
- Self-supervised learning (SSL) can address this challenge by **pre-training a neural network encoder** with unlabeled data, followed by fine-tuning for a downstream task with limited labels. Hence, we integrate CL to FL as **federated contrastive learning**. 
- Simply applying CL to each client and then aggregating the models is not the optimal solution for the following two reasons:
  - Each client only has a small amount of unlabeled data with limited diversity. 
  - If each client only focuses on CL on its local data while not considering other's data, each data will have its own feature space based on its raw data.
- Previous works on FL is that fully labeled data are needed to perform FL, which results in high-labeling cost.
- Previous works on CL
  - Drawback: CL is designed for centralized learning on large-scale datasets with sufficient data diversity. However, when applying CL to FL, the limited data diversity will greatly degrade the performance of the learned model. 
### Contributions
- Proposed an FCL framework for volumetric medical image segmentation with limited annotations
- No data sharing for Federated Learning and leverage the structural similarity of images among clients.
### Methodology
- The FCL consists of two stages (1) feature exchange, (2) global structure matching
- The feature exchange simply exchange the features of its local data with other clients. It provides more diverse data to compare with for better local contrastive learning while avoiding raw data sharing. 
- The global structure matching (GSM) leverages structural similarity of 3D images to align similar features among clients for better FCL.
- Used **<code>MoCo</code>** for local CL since it has a memory bank for negatives, which can leverage local and remote features.
- Two encoders: (1) Main encoder will be used as the initialization for fine-tuning, (2) Momentum encoder as the variation of the main encoder to generate contrast features. The momentum encoder generates local features from local images - used as local negatives. 


## [2] Contrastive Semi-supervised Learning for Domain Adaptive Across Similar Anatomical Structures
### Problem Statement
- Existing Contrastive Learning models are challenged by the cross-anatomy domain shift due to the different appearance and even imaging modalities from the target modalities. 
### Clinical Motivations
- CNN models require a large amount of manual annotations for training images, which is highly timely-consuming and labor-intensive to collect. Therefore, it is desirable to reduce manual annotations for model training while maintaining the segmentation performance. 

- CNNs have achieved remarkable progress in medical image segmenation. However, **it requires a large amount of manual annotations for training images, which is highly time-consuming and labor-intensive to collect**. Therefore, the authors desired to reduce the manual annotations using Semi-Supervised Learning, as it only requires a small set of labeled data with the availability of a large set of unlabeled data. Nonetheless, it is still challenging for most existing SSL methods to achieve high performance with a small training set. 

### Technical Motivations
- Previous works on **Domain Adaptation**: adapting a model trained with annotated heart MRI to segment heart CT images.
  - To alleviate the performance gap between the two domains, a widely-used method is to fine-tune the pre-trained models with target domains.
- Previous works on **Supervised Domain Adaptation**: adapting a model trained with a source domain to a target domain. 
- SOTA DA methods for medical image segmentation require that the source and target domains have the same set of anatomical structures even they can be from different imaging modalities. 
### Contributions
- Reduce the annotation cost and overcome the problem of limited training images in a target domain, the authors proposed to leverage a dataset for training. They poposed CS-CADA as a generalization of existing semi-supervised and domain adaptation methods.
- The authors adopted Domain-Specific Batch Normalization to deal with domain shift between the two domains while transferiing knowledge of similar anatomical structures.
- The authors investigated the cross-anatomy semi-supervised domain adaptaion for medical image segmentation. Compared with the existing domain adaptaion methods that require the segmented objects to be the same in the source and target domains, their method relaxes this requirement and enables adapting a model to segment a different anatomical structure to the source domain.
- Formulated a regularization framework to leverage unannotated images in the target domain.

### Methodology
1. Joint Learning with Domain-Specific Batch Normalization 
- Convolutional kernels are shared across domains $S$ and $T$ to learn general representations for the similar anatomical representations.
- They introduced DBSN block to the network that consists of two types of Batch Normalization and each of them is in charge of one domain to effectively tackle the inter-domain discrepancy.
2. Self-ensembling Mean Teacher with DBSN
- SE-MT to exploit unannotated images in the target domain
3. Cross Domain Contrastive Learning
- To capture domain-invariant features for the similar anatomical structures while being robust against the different image styles. 