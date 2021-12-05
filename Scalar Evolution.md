# Scalar Evolution
1. Change in the value of scalar variables over iterations of the loop
2. Powerful symbolic technique

# Example for scalar evolution usage
```
void foo(int *a, int n, int k) {
  int t = 0;
  for (int i = 0; i < n; i++){
    t = t + k;
    *a = t;
  }
}
```

before
```
1. for.body:
2.   %i = phi i32 [ %inc, %for.body ], [ 0, %for.body.preheader ]
3. %t = phi i32 [ %tk, %for.body ], [ 0, %for.body.preheader ]
4.   %tk = add nsw i32 %t, %k
5.   %inc = add nuw nsw i32 %i, 1
6. %cmp = icmp slt i32 %inc, %n
7.   br i1 %cmp, label %for.body, label %for.cond.cleanup
...
for.cond.cleanup:
%tk.final = phi i32 [ 0, %entry ], [ %tk, %for.body]
8.  store i32 %tk.final, i32* %a
```

*** IR Dump After Induction Variable Simplification ***
```
...
1’. for.cond.cleanup.loopexit:
2’.   %tk.final = mul i32 %n, %k
3’. store i32 %tk.final, i32* %a
```


```
int foo(int *a, int n, int k) {
  for (int i = 0; i < n; i++){
    a[i] = i*k;
  }
}
```
```
1. *** IR Dump After Canonicalize natural loops ***
2. for.body:                                         
3.   %i = phi i32 [ %inc, %for.body ], [ 0, %for.body.preheader ]
4.   %ik = mul nsw i32 %i, %k
5.  %arrayidx = getelementptr inbounds i32, i32* %a, i32 %i
6.   store i32 %ik, i32* %arrayidx
```

```
1’. *** IR Dump After Loop Strength Reduction ***
2’. for.body:
3’.   %IV_IK = phi i32 [ 0, %for.body.preheader ], [ %IV_IK_plus_K, %for.body ]
4’.   store i32 %IV_IK, i32* %lsr.iv
5’.   %lsr.iv.next = add i32 %lsr.iv7, -1
6’.   %IV_IK_plus_K = add i32 %IV_IK, %k
```

# Induction Variable
## Basic Induction Variable (BIV):
• Increases or decreases by a constant on each iteration of the loop
## Generalized Induction Variable (GIV)
• Update value is not constant
• Dependent on other BIVs/GIVs (linear, non-linear), multiple update

# Basic Recurrence
{init, direction, delta}
```
int foo(int *a, int n, int k){
  for (int i = 0; i < n; i++)
    a[i] = i*k;
}
1. Printing analysis 'Scalar Evolution Analysis' for function 'foo':
2. Classifying expressions for: @foo
3. ... 
4. %mul = mul nsw i32 %i, %k
5. -->  {0,+,%k}<%for.body>  Exits: ((-1 + %n) * %k) 
6. ...
```

# Chain Recurrence
{1, +, {3, +, 7}} => {1,+,3,+,7}

```
void foo(int *p, int n){
  for( int x = 0; x < n; x++)
    p[x] = x*x*x  + 2*x*x + 3*x + 7;   
}
IV Chain#0 Head: (store i32 %add6, i32* %arrayidx.) SCEV={7,+,6,+,10,+,6}<%for.body>
```

```
void foo(int *p, int n) {
  int t0 = 7;
  int t1 = 6;
  int t2 = 10;
  for(int x = 0; x < n; x++) {
    p[x] = t0;
    t0 = t0 + t1;
    t1 = t1 + t2;
    t2 = t2 + 6;
    //p[x] = x*x*x  + 2*x*x + 3*x + 7;
  }
}
```

# SCEV Rewriting / Folding
Expression Rewrite Example
𝐺+ 𝑒,+,𝑓 ⇒ 𝐺+𝑒,+,𝑓 12+ 7,+,3 ⇒ 19,+,3
𝐺∗ 𝑒,+,𝑓 ⇒ 𝐺∗𝑒,+,𝐺∗𝑓 12∗ 7,+,3 ⇒ 84,+,36
𝑒,+,𝑓 + 𝑔,+,ℎ ⇒ 𝑒+𝑔,+,𝑓+ℎ 7,+,3 + 1,+,1 ⇒ 8,+,4
𝑒,+,𝑓 ∗ 𝑔,+,ℎ
⇒ 𝑒∗𝑔,+,
𝑒∗ℎ+𝑓∗𝑔+𝑓∗ℎ,
+,2∗𝑓∗ℎ
0,+,1 ∗ 0,+,1 ⇒ 0,+,1,+,2

# Usage
## Canonical Loop
## Loop Strength Reduce(LSR)
Hoist loop invariant computation outside loop
replace multiply with add

1. Check Loop Form
2. Collect Chains
3. Collect Types and Factors
4. Generate Formulas
5. Solve and Implement
## Dependence Analysis
## Vectorize
© 2017 Arm Limited 48 © 2018 Arm Limited   
SCEV User - Vectorizers
Vectorizers –Loop Vectorizer, SLP, Load-Store Vectorizer
Use SCEV for 
- Induction variable (step loop-invariant)
- Trip count
- Loop Access Analysis
