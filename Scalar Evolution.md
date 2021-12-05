# Scalar Evolution
1. Change in the value of scalar variables over iterations of the loop
2. Powerful symbolic technique

# Example for scalar evolution usage
```
void foo(int *a, int n, int k) {
  int t = 0;
  for (int i = 0; i < n; i++)
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
  for (int i = 0; i < n; i++)
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
