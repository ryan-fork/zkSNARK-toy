# zkSNARK-toy

This is a toy exmaple about how to use [libsnark](https://github.com/scipr-lab/libsnark). libsnark implements zkSNARK algorithm. This toy example do the following:

- The prover know a merkle tree root `rt`, a leaf `leaf`, a valid merkle branch `path` from `leaf` to `rt`, and `prev_leaf`, the relation between `prev_leaf` and `leaf` is : `prev_leaf = sha256(leaf)`.

- The verifier is given `rt` and `prev_leaf`, can verify that prover know a valid preimage of `prev_leaf`, and a valid merkle brach `path` from `leaf` to `root` using zkSNARK algorithm.  

### howto

`./get-libsnark && make && ./main`

### details

see [here](https://blockchain.iethpay.com/libsnark-example.html) for details.

### performance

- proof generation time : 10.297805s

- proof verification time : 0.041092s

- proof size : 2294 bits == 7 G1 and 1 G2 element. sizeof(G1 element) = sizeof(x,y) = 512 can be compressed into x coordinate and flag(8 bits) which represent y coordinate even or odd，so sizeof(compressed G1) = 264;
Similarly, sizeof(compressed G2 element) = sizeof (x coordinate of G2) + 8 bits = 520 bits, so proof size is 2368 bits. the output of libsnark is different, see https://github.com/zcash/zips/issues/43


## 代码详解
gadget 是构建 R1CS 实例的基础组件，gadget 是个基类，任何针对具体功能的 gadget 都继承自 gadget，比如说针对 sha256 功能的 sha256_compression_function_gadget、针对 Merkle 树的 merkle_tree_check_read_gadget 等。组合使用这些基本的 gadget，我们构建出复杂的 R1CS 实例。

### 代码结构
- src/gadget.hpp : 提供了 toy_gadget 类，使用其它基础 gadget 构建 zkSNARK-toy 相关的 constraint 与 witness

- src/snark.hpp : 提供对密钥生成、零知识证明生成以及验证零知识证明的简单封装，在 main.cpp 中使用

### 使用到的关键 gadget
- sha256_compression_function_gadget : 进行 sha256 哈希运算时用到的 gadget

- merkle_authentication_path_variable : 保存了 merkle 分支

- merkle_tree_check_read_gadget : 进行 merkle 分支验证的 gadget