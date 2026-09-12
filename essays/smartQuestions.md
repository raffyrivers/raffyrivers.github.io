---
layout: essay
type: essay
title: "Smart Questions"
# All dates must be YYYY-MM-DD format!
date: 2026-09-10
published: true
---

### Introduction 

Questions are a vital part of learning. This makes Questions super important in the world of Software 
engineering. This may be obvious, but there are questions that can hurt a persons learning and waste 
others time. This is why it is important to ask smart questions that are on topic, clear, thought 
provoking, and unique.  I will be discussing some examples of "smart" questions and "not smart" questions.

### Smart Question

>I'm new to OpenGL and I'm a little overwhelmed with all of the random functions that I have in my code. They work and I know when to use them, but I don't know why I need them or what they actually do.

>I know that `glLoadIdentity()` replaces the current matrix with the identity matrix, but what exactly does that do? If every program requires it, why isn't the identity matrix by default unless otherwise specified? I don't like to have functions in my code unless I know what they do. I should note that I am using OpenGL exclusively for rich 2D clients so excuse my ignorance if this is something very obvious for 3D.

>Also a little confused about `glMatrixMode(GL_PROJECTION)` VS `glMatrixMode(GL_MODELVIEW)`.

This is an example of "smart" question. It is in the appropriate section of Stack Overflow for the OpenGL API
and graphics. Its an original thought provoking question on a specific function. The question asks 
why these function are needed and what they do. Eliciting programmers to ask themselves this question 
and understand these functions enough to explain it thouroghly to the asker. Making this a good question by Eric Raymond's standards.

#### Answer 

>The identity matrix, in terms of the projection and modelview matrices, essentially resets the matrix back to its default state.

>As you hopefully know, glTranslate and glRotate are always relative to the matrix's current state. So for instance, if you call glTranslate, you are translating from the matrix's current 'position', not from the origin. But if you want to start over at the origin, that's when you call glLoadIdentity(), and then you can glTranslate from the matrix which is now located at the origin, or glRotate from the matrix which is now oriented in the default direction.

>I think Boon's answer, that it is the equivalent of 1, is not exactly correct. The matrix actually looks like this:
```
1 0 0 0
0 1 0 0
0 0 1 0
0 0 0 1
```
>That is the identity matrix. Boon is correct, mathematically, that any matrix multiplied with that matrix (or a matrix that looks like that; diagonal ones, all else 0s) will result in the original matrix, but I don't believe he explained why this is important.

>The reason why this is important is because OpenGL multiplies all positions and rotations through each matrix; so when for instance you draw a polygon (glBegin(GL_FACE), some points, glEnd()), it translates it to "world space" by multiplying it with the MODELVIEW, and then translates it from 3D to 2D by multiplying it with the PROJECT matrix, and that gives it the 2D points on screen, along with the depth (from the screen 'camera'), which it uses to draw pixels. But when one of these matrices are the identity matrix, the points are multiplied with the identity matrix and therefore are not changed, so the matrix has no effect; it does not translate the points, it does not rotate them, it leaves them as-is.

>I hope this clarifies a bit more!

***

This answer provides an detailed and thourough response from another programmer with a better understanding of these OpenGL functions and is able to explain it to the asker and help them understand the functions better. This response not only answers the askers question but answers any future programmers
with the same or similar question. 

### Not Smart Question
***
enter image description hereI am solving a binary tree traversal problem on a university eLab programming platform.

The problem is:

>Given the preorder and inorder traversals of a binary tree with n distinct nodes, output its postorder traversal.

The constraints are:

`1 ≤ n ≤ 10^5`

The nodes are numbered from 1 to n.
Example

input:
```
5
5 3 2 1 4
3 5 1 2 4
```
Expected output:

`3 1 4 2 5`

Another test case:
```
6
6 5 3 2 1 4
6 3 5 1 2 4
```
Expected output:

`3 1 4 2 5 6`

My implementation uses the standard reconstruction idea, but instead of recursion I use an iterative stack, because `n` can be as large as `100000`.

Here is the exact code I submitted:
```
#include <stdio.h>
#define MAXN 100000

typedef struct{int l,r,s,v,p;}F;

int main(){
    int n,i,pi=1,top=0;
    static int pre[MAXN+1],pos[MAXN+1];
    static F st[MAXN*2];

    if(scanf("%d",&n)!=1)return 0;
    for(i=1;i<=n;i++)scanf("%d",&pre[i]);
    for(i=1;i<=n;i++){int x;scanf("%d",&x);pos[x]=i;}

    st[top++]=(F){1,n,0,0,0};

    while(top){
        F *f=&st[top-1];
        if(f->l>f->r){top--;continue;}

        if(!f->s){
            f->v=pre[pi++];f->p=pos[f->v];f->s=1;
            st[top++]=(F){f->l,f->p-1,0,0,0};
        }else if(f->s==1){
            f->s=2;
            st[top++]=(F){f->p+1,f->r,0,0,0};
        }else{
            printf("%d ",f->v);top--;
        }
    }

    printf("\n");
    return 0;
}
```
The unusual problem

The code passes the visible logical test cases, but eLab reports that Logical Test Case 4 fails.

The result is approximately:
```
Logical T1 : PASS
Logical T2 : PASS
Logical T3 : PASS
Logical T4 : FAIL

Mandatory Test Case : PASS

Cyclomatic Complexity : PASS
Token Count           : PASS
NLOC                   : PASS

Overall : 87%

The eLab challenge also requires the following mandatory keyword:

for(i=1;i<=n;i++)

and the complexity-related limits shown by the platform are:

Cyclomatic Complexity : 8
Token Count            : 390
NLOC                   : 41
```
My submission satisfies those checks.
My understanding of the algorithm

The first value of preorder is the root.

Using the inorder position of that root:

left subtree  = inorder[l ... p-1]
right subtree = inorder[p+1 ... r]

I then process the left subtree, followed by the right subtree, and finally print the root, which produces postorder.

The pos[] array makes finding the root position O(1), so the overall intended complexity is O(n).
What I am trying to determine

Since the same implementation passes the visible tests and all the non-logical checks, I am trying to understand what could make only one hidden logical test fail.

Could there be an edge case that I am missing, such as:

* a completely left-skewed tree

* a completely right-skewed tree

* a tree containing only one node

* a highly unbalanced tree

* subtree boundary/index errors

* an issue with the iterative stack state

* an issue caused by `n = 100000`

* or an issue with the eLab test/checker itself?

I would especially like to know whether there is anything incorrect in the implementation above that could produce a wrong answer for a valid preorder/inorder pair, despite passing the other logical tests.

I am not looking only for the standard binary-tree traversal algorithm; I am specifically trying to identify why this particular implementation could fail Logical T4 on eLab.

I have verified it against the stated examples and other valid cases but yet can't solve it

***

This question is not to bad. However, the code the programmer provides is messy and unorganized in a way that 
even the most seasoned programmers would have trouble reading it. The question is also a too long and 
could possibly be shortened to better get his question across. This question did not receive an answer.

(This change was made from my personal computer using github desktop)



