---
id: kt:aws-trainium-and-inferentia
type: Feature
title: AWS Trainium and Inferentia
aliases: [AWS Trainium, AWS Inferentia, Trainium, Inferentia, Trainium2, Trainium3, Inferentia2]
domain: ai-and-ml
summary: >-
  AWS's own families of machine learning accelerator chips: Trainium (now in its third generation)
  for training and large-scale inference, and Inferentia for cost-efficient inference, offered
  through EC2 Trn and Inf instances and programmed with the AWS Neuron SDK.
verified: 2026-11-12
status: current
broader: [kt:amazon-ec2, kt:generative-ai-on-aws]
related: [kt:aws-graviton, kt:amazon-sagemaker-ai, kt:amazon-bedrock, kt:amazon-eks, kt:eks-auto-mode]
relations:
  - {predicate: dependsOn, object: kt:aws-neuron-sdk}
  - {predicate: dependsOn, object: kt:aws-nitro-system}
  - {predicate: alternativeTo, object: kt:ec2-gpu-instances}
  - {predicate: pricedBy, object: kt:ec2-capacity-blocks-for-ml}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "AWS Trainium"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ai/machine-learning/trainium/
    type: official-docs
    accessed: 2026-11-12
  - id: s2
    title: "Announcing Amazon EC2 Trn3 UltraServers for faster, lower-cost generative AI training"
    publisher: Amazon Web Services (What's New with AWS)
    url: https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-ec2-trn3-ultraservers/
    type: official-blog
    accessed: 2026-11-12
  - id: s3
    title: "Amazon EC2 Trn3 Architecture"
    publisher: Amazon Web Services (AWS Neuron Documentation)
    url: https://awsdocs-neuron.readthedocs-hosted.com/en/latest/about-neuron/arch/neuron-hardware/trn3-arch.html
    type: official-docs
    accessed: 2026-11-12
  - id: s4
    title: "Amazon EC2 Trn2 Instances and Trn2 UltraServers for AI/ML training and inference are now available"
    publisher: Amazon Web Services (AWS News Blog)
    url: https://aws.amazon.com/blogs/aws/amazon-ec2-trn2-instances-and-trn2-ultraservers-for-aiml-training-and-inference-is-now-available/
    type: official-blog
    accessed: 2026-11-12
  - id: s5
    title: "Amazon EC2 Inf2 Instances"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/ec2/instance-types/inf2/
    type: official-docs
    accessed: 2026-11-12
  - id: s6
    title: "Use AWS Inferentia instances with Amazon EKS for Machine Learning"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/eks/latest/userguide/ml-inferentia.html
    type: official-docs
    accessed: 2026-11-12
created: 2026-11-12
---

# AWS Trainium and Inferentia

AWS Trainium and AWS Inferentia are machine learning accelerator chips that AWS designs itself. Trainium is positioned for both training and high-volume inference of large models, with AWS pitching it on cost per token and price performance at scale.[^s1] Inferentia is built specifically for deep learning inference, including generative AI models such as LLMs and vision transformers.[^s5] Neither is a standalone service: you use them by launching EC2 Trn or Inf instances (directly, or through Amazon EKS, Amazon ECS, AWS Batch or SageMaker HyperPod) and compiling models with the AWS Neuron SDK.[^s1][^s5]

## Generations

**Trainium2** became generally available in Trn2 instances in December 2024. Each trn2.48xlarge has 16 Trainium2 chips (8 NeuronCores and 96 GiB of HBM per chip), 192 vCPUs, 2 TiB of memory and 3.2 Tbps of EFA v3 networking, delivering up to 20.8 PFLOPS of dense FP8 compute.[^s4] AWS described Trn2 as 4x faster than first-generation Trn1 and claimed 30–40% better price performance than GPU-based P5e and P5en instances.[^s4] Trn2 UltraServers, which join four Trn2 instances (64 chips) over NeuronLink, launched in preview at the same time.[^s4]

**Trainium3**, AWS's first 3 nm AI chip, reached general availability in Trn3 UltraServers on 2 December 2025.[^s2] Each chip offers 2.52 PFLOPS of FP8 compute and 144 GB of HBM3e at 4.9 TB/s, which AWS gives as 1.5x the capacity and 1.7x the bandwidth of Trainium2, and supports MXFP8 and MXFP4 formats.[^s2] A fully configured Trn3 UltraServer holds up to 144 chips with 362 FP8 PFLOPS and 20.7 TB of HBM3e, joined by the NeuronSwitch-v1 all-to-all fabric; AWS claims up to 4.4x the performance and 4x the performance per watt of Trn2 UltraServers.[^s2] The Neuron architecture guide describes two UltraServer layouts: Gen1 with 64 chips across 4 servers and Gen2 with 144 chips across 36 servers, each chip having 2,560 GB/s of NeuronLink-v4 bandwidth.[^s3] UltraServers can be grouped into EC2 UltraClusters 3.0 that scale to hundreds of thousands of chips.[^s2]

**Inferentia2** powers Inf2 instances, with up to 12 chips, 384 GB of shared accelerator memory (32 GB HBM per chip) and 9.8 TB/s of memory bandwidth per instance.[^s5] AWS claims up to 4x higher throughput and up to 10x lower latency than first-generation Inf1.[^s5] Inf2 is unusual among inference instances in linking chips with NeuronLink (192 GB/s), so the larger sizes can shard one model across chips.[^s5] It supports FP32, TF32, BF16, FP16, UINT8 and configurable FP8.[^s5]

## How you use them

Models run through the Neuron SDK, which compiles them for NeuronCores. AWS lists PyTorch (including FSDP and TorchTitan), vLLM, Hugging Face Transformers and Ray among supported tools, and offers the Neuron Kernel Interface (NKI) for hand-written kernels plus profiling and monitoring tools.[^s1] On Amazon EKS, the EKS-optimized accelerated AMI ships the Neuron driver, and you add the Neuron device plugin or, on Kubernetes 1.34 and later, the recommended Neuron DRA driver; pods then request `aws.amazon.com/neuron` devices or `aws.amazon.com/neuroncore` cores and need a toleration for the taint on Neuron nodes.[^s6]

## Pricing and gotchas

Trn and Inf instances are billed as ordinary EC2 instances; at the Trn2 launch AWS offered them through EC2 Capacity Blocks for ML, reservable for up to 64 instances and six months, and only in US East (Ohio).[^s4] Check each instance page and the console for current Regions and prices, since the Trn3 pages do not list them.[^s2][^s3]

- Compiling a model at pod start-up can take minutes; AWS recommends pre-compiling and caching artifacts.[^s6]
- Neuron container images are several GB, so size node volumes accordingly.[^s6]
- TensorFlow Neuron and MXNet Neuron are no longer actively developed; plan on PyTorch, JAX or vLLM.[^s6]
- A model must fit in the accelerator memory allocated to its pod, and multi-device pods on the larger Inf2 sizes need contiguous device IDs.[^s6]
- Price-performance figures are AWS's own claims; benchmark your model before committing.[^s2][^s4]

## When to use them (and when not)

Choose Trainium for large-scale training or high-throughput inference where cost per token matters and your stack runs on PyTorch, JAX or vLLM.[^s1][^s6] Inferentia2 fits steady, cost-sensitive inference of models up to the size that fits in one instance's 384 GB.[^s5] Stay on GPU instances when you depend on CUDA-specific libraries or kernels that Neuron does not support. If you just want hosted models, Amazon Bedrock runs on this hardware for you; AWS reports Trainium3 serving Bedrock workloads up to 3x faster than Trainium2.[^s2]

## Connections

Both chip families sit under **Amazon EC2**, as accelerated instance types, and under **generative AI on AWS**.[^s1][^s5] They **depend on** the **AWS Neuron SDK** for compilation and on the **AWS Nitro System**, which AWS lists as part of the co-designed stack alongside **AWS Graviton** host CPUs.[^s1] They are an **alternative to** GPU-based EC2 instances such as P5e and P5en.[^s4] Capacity is commonly **priced by** **EC2 Capacity Blocks for ML** and **scoped to** specific **AWS Regions**.[^s4] **Amazon SageMaker AI** (HyperPod), **Amazon EKS** and **EKS Auto Mode** orchestrate them, and **Amazon Bedrock** uses Trainium for hosted inference.[^s1][^s2][^s6]

## Sources
[^s1]: Amazon Web Services, "AWS Trainium". https://aws.amazon.com/ai/machine-learning/trainium/ (accessed 2026-11-12)
[^s2]: Amazon Web Services, What's New with AWS, "Announcing Amazon EC2 Trn3 UltraServers for faster, lower-cost generative AI training" (2025-12-02). https://aws.amazon.com/about-aws/whats-new/2025/12/amazon-ec2-trn3-ultraservers/ (accessed 2026-11-12)
[^s3]: Amazon Web Services, AWS Neuron Documentation, "Amazon EC2 Trn3 Architecture". https://awsdocs-neuron.readthedocs-hosted.com/en/latest/about-neuron/arch/neuron-hardware/trn3-arch.html (accessed 2026-11-12)
[^s4]: Amazon Web Services, AWS News Blog, "Amazon EC2 Trn2 Instances and Trn2 UltraServers for AI/ML training and inference are now available" (2024-12-03). https://aws.amazon.com/blogs/aws/amazon-ec2-trn2-instances-and-trn2-ultraservers-for-aiml-training-and-inference-is-now-available/ (accessed 2026-11-12)
[^s5]: Amazon Web Services, "Amazon EC2 Inf2 Instances". https://aws.amazon.com/ec2/instance-types/inf2/ (accessed 2026-11-12)
[^s6]: Amazon Web Services, "Use AWS Inferentia instances with Amazon EKS for Machine Learning", Amazon EKS User Guide. https://docs.aws.amazon.com/eks/latest/userguide/ml-inferentia.html (accessed 2026-11-12)
