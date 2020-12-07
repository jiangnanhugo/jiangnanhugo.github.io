---
layout: "post"
title: "How to Optimize GEMM"
date: "2018-10-10 19:05"
Comments: true
---
`GEMM` stands for general matrix-matrix operation.
## The first version
```c
// create macros so that the matrices are stored in column-major order.
#define A(i,j) a[ (j) * lda + (i) ]

#define B(i,j) b[ (j) * ldb + (i) ]

#define C(i,j) c[ (j) * ldc + (i) ]

// C= A * B + C
void gemm(int m, int n, int k, double *a, int lda,
                               double *b, int ldb,
                               double *c, int ldc){
  for(int i=0;i<m;++i){             // Loop over the rows of C
    for(int j=0;j<n;++j){           // Loop over the columns of C
      for(int p=0;p<k;++p){         // update C(i,j) with the inner
        C(i,j) += A(i,p) * B(p,j);  // product of the i-th row of A
      }                             // and the j-th column of B.
    }
  }
}
```
