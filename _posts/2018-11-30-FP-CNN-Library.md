### Fast CPU-based Multicore Parallel Fixed-point CNN Simulation Library

The motivation for this project is creating a general library which can simulate a CNN with fixed-point operations fast. Since **Python** is poor in fixed-point calculation support, I try to write the project in MATLAB thoroughly. While the fi object in MATLAB can conviniently express fixed-point(FP) operations, there are still many functions in MATLAB that don't support fi. So I have to rewrite a lot of basic functions such as conv2d etc step by step.

### Project Progress
Several fundamental functions have been completed and carried on in a parallel way as possible. The comprehensive review of all functions as below:

<center>

|        **Type**         |       **Status**     |        **Description**          |
|:-----------------------:|:--------------------:|:-------------------------------:|
| Conv2d                  |      Completed       |                                 |
| Depthwise Conv          |      Completed       |                                 |
| Pooling                 |      Completed       |           MAX/AVG*              |
| ReLU                    |      Completed       |                                 |
| FC                      |      Completed       | By converting FC to Conv or MM  |
| Softmax                 |     Uncompleted      |        Working on it            |
| Pointwise Conv          |      Completed       |  Can be replaced by Conv2d**    |

</center>

##### * Now you can use self-defined pooling function other than MAX/AVG. Before applying customed pooling function, you should add the function name and its definition in the Pool_Type register table.

##### ** Pointwise Conv2d can be implemented by Conv2d, but it will take a lot of operations to reordering elements by doing this through Conv2d. So I'm trying to find a more efficient way to directly apply Pointwise Conv2d. 

### Details

- The parallelism level (PL) of the source code is from vector to tensor. The definition of different levels is as below:
  - L1 Vector: 1×N array
  - L2 Matrix: M×N matrix
  - L3 Tensor: M×N×[H1,H2 ...], where the length of [H] is no less than 1.
  
  Higher the PL is, faster the function runs.

- Conv2d calculates 2d convolution of the input tensor, PL is L3, the input and output format are TF-compatible.
- Pooling calculates 2d pooling of the input tensor, PL is L3, while pooling function doesn't support 3d pooling like TF.
- Up to present, a standard ConvNet like MobileNet without ResBlock can run on this library. The MobileNet which has 28 layers with depthwise and pointwise convolution consumes about 3~5 minutes to forward once on a 224×224×3 input image @Intel Core i5-8400 CPU with 16.0 GB RAM.
- NOTE: If your MATLAB support parallel computing with **Parallel Computation Toolbox (PCT)**, the algorithm will become faster. According to my test, MobileNet will save about 65% time of non-parallel version, which means it takes 70 seconds to forward total 28 layers once. More cores you have on your PC, faster it runs.
- The acceleration mainly due to the **Im2col - Reshape - GEMM - Reshape - Output** procedure as below:

<center>
<img src="https://github.com/JackGittes/FixedCNN/blob/master/Doc/img/details.png" width=700>
</center>

### Requirement

All codes are tested in **MATLAB R2017b** and don't support GPU acceleration, which means you should only run it on small dataset otherwise the runtime will be quite scaring. As I say above, this library is designed for simulating FP-CNN and it's also an experimental research which helps us understand FP behaviors of deep neural networks. Furthermore, it can help people who want to deploy their CNN algorithms on FP devices (FPGA/ASIC etc) to verify the effectiveness of quantization method.

### TODO

- Although the MATLAB library can implement a simple FP-CNN for now, there are still many problems in this code. I will fix these problems in the future.

- To enhance the library's robustness and compatibility, an elaborate and overall unit test module is under construsting which will check more complicated conditions in application scenarios.

- I try my best to design every function to be similar to TensorFlow-style as possible so that people who are familiar with TensorFlow can easily tranfer to this library without much learning effort.
