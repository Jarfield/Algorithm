# Greedy Algorithm

## An Activity Selection Problem

### Problem Description

- **Set of Activities**  
  Suppose we have a set \( S = \{a_1, a_2, \ldots, a_n\} \) of \( n \) proposed activities that wish to use a resource, such as a lecture hall, which can serve only one activity at a time.

- **Activity Times**  
  Each activity \( a_i \) has a **start time** \( s_i \) and a **finish time** \( f_i \).  
  The times satisfy \( 0 \leq s_i < f_i < \infty \).  
  If selected, activity \( a_i \) takes place during the half-open time interval \([s_i, f_i)\).

- **Activity Compatibility**  
  Two activities \( a_i \) and \( a_j \) are **compatible** if their time intervals do not overlap.  
  Mathematical expression:  
  \[
  a_i \text{ and } a_j \text{ are compatible} \iff s_i \geq f_j \text{ or } s_j \geq f_i
  \]

- **Objective**  
  In the activity-selection problem, the goal is to **select a maximum-size subset of mutually compatible activities**.

- **Assumption**  
  We assume that the activities are sorted in monotonically increasing order of finish time:  
  \[
  f_1 \leq f_2 \leq \cdots \leq f_n
  \]

- **Example**

| Activity \( a_i \) | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 |
|--------------------|---|---|---|---|---|---|---|---|---|----|----|
| **Start Time \( s_i \)** | 1 | 3 | 0 | 5 | 3 | 5 | 6 | 8 | 8 | 2 | 12 |
| **Finish Time \( f_i \)** | 4 | 5 | 6 | 7 | 9 | 9 | 10 | 11 | 12 | 14 | 16 |

- **Explanation:**

  - **Mutually Compatible Subset {a₃, a₉, a₁₁}:**
    - **a₃:** [0, 6)
    - **a₉:** [8, 12)
    - **a₁₁:** [12, 16)
  
    These activities do not overlap in time and are therefore mutually compatible. However, this subset is **not** the maximum possible.

  - **Maximum Subset {a₁, a₄, a₈, a₁₁}:**
    - **a₁:** [1, 4)
    - **a₄:** [5, 7)
    - **a₈:** [8, 11)
    - **a₁₁:** [12, 16)
  
    This subset includes four mutually compatible activities, which is larger than the previous subset.

  - **Another Maximum Subset {a₂, a₄, a₉, a₁₁}:**
    - **a₂:** [3, 5)
    - **a₄:** [5, 7)
    - **a₉:** [8, 12)
    - **a₁₁:** [12, 16)
  
    This is another example of a largest subset of mutually compatible activities.

### Solution

#### Dynamic Programming Method

- **Problem Definition**

    Define the set \( S_{i,j} = \{a_k \in S \mid f_i \leq s_k < f_k \leq s_j\} \), which is the subset of activities in \( S \) that can start after activity \( a_i \) finishes and finish before activity \( a_j \) starts.

    Add fictitious activities \( a_0 \) and \( a_{n+1} \), and adopt the conventions that \( f_0 = 0 \) and \( s_{n+1} = \infty \), thus \( 0 \leq i, j \leq n+1 \).

    Therefore, the subproblem is to select a maximum-size subset of mutually compatible activities from \( S_{i,j} \), for \( 0 \leq i < j \leq n+1 \).

- **Optimal Substructure**

    The optimal substructure of this problem is as follows:

  - Suppose that an optimal solution \( A_{i,j} \) to \( S_{i,j} \) includes activity \( a_k \).
  - Then the solutions \( A_{i,k} \) to \( S_{i,k} \) and \( A_{k,j} \) to \( S_{k,j} \) used within  this optimal solution to \( S_{i,j} \) must be optimal as well.
  - Thus, we can transform the problem of building a maximum-size subset of mutually compatible   activities in \( S_{i,j} \) into finding maximum-size subsets \( A_{i,k} \) and \( A_{k,j} \) of  mutually compatible activities in \( S_{i,k} \) and \( S_{k,j} \), respectively.
  - Then forming the solution \( A_{i,j} \) as:
      \[
      A_{i,j} = A_{i,k} \cup \{a_k\} \cup A_{k,j}
      \]

- **Recurrence Relation**

    Let \( c[i,j] \) be the number of activities in a maximum-size subset of mutually compatible activities in \( S_{i,j} \) (with \( c[i,j] = 0 \) for \( i \geq j \)).

    For a nonempty subset \( S_{i,j} \), if \( a_k \) is used in an optimal solution, we have the recurrence (assuming \( k \) is given):
    \[
    c[i,j] = c[i,k] + c[k,j] + 1
    \]
    However, since we don’t know the value of \( k \), we need to look it up in the range from \( i+1 \) to \( j-1 \) to find the best. Thus:
    \[
    c[i,j] =
    \begin{cases}
    0 & \text{if } S_{i,j} = \emptyset, \\
    \max_{i < k < j} \{ c[i,k] + c[k,j] + 1 \} & \text{if } S_{i,j} \neq \emptyset.
    \end{cases}
    \]

#### Making the Greedy Choice

Based on the dynamic-programming process, we should write a tabular, bottom-up algorithm based on the recurrence above.

- **Theorem 16.1**

    Consider any nonempty subproblem \( S_{i,j} \), and let \( a_m \) be the activity in \( S_{i,j} \) with the earliest finish time, then:

  1. **\( a_m \) is used in some maximum-size subset of mutually compatible activities of \( S_{i,j} \).**
  2. **The subproblem \( S_{i,m} \) is empty, so that choosing \( a_m \) leaves \( S_{m,j} \) as the only subproblem that may be nonempty.**

- **Proof of Theorem 16.1**

  1. **Proving \( S_{i,m} \) is empty:**
     - Suppose \( S_{i,m} \) is not empty. Then there exists some activity \( a_k \) such that \( f_i \leq s_k < f_k \leq s_m < f_m \).
     - This implies \( a_k \) is also in \( S_{i,j} \) and its finish time is earlier than \( a_m \), which contradicts our choice of \( a_m \) as the activity with the earliest finish time.
     - Therefore, \( S_{i,m} \) must be empty.

  2. **Proving \( a_m \) is used in some optimal solution:**
     - Suppose \( A_{i,j} \) is a maximum-size subset of mutually compatible activities of \( S_{i,j} \).
     - The activities in \( A_{i,j} \) are ordered in monotonically increasing order of finish time, and let \( a_k \) be the first activity.
       - If \( a_k = a_m \), we are done.
       - If \( a_k \neq a_m \), construct the subset \( A'_{i,j} = A_{i,j} - \{a_k\} \cup \{a_m\} \).
       - The activities in \( A'_{i,j} \) are disjoint and it has the same number of activities as \( A_{i,j} \).
       - Thus, \( A'_{i,j} \) is a maximum-size subset of mutually compatible activities of \( S_{i,j} \) that includes \( a_m \).

- **Implementation**

    ```cpp
    std::vector<int> RecursiveActivitySelector(const std::vector<Activity>& activities, int k, int n) {
        /*
        k: The index of the last selected activity.
        n: The total number of real activities.
        */
        int m=k+1;
        while(m<=n&&activities[m].start<=activities[k].finish){
            m++;
        }
        if(m<=n){
            // Include activity m and recursively select the next compatible activities
            std::vector<int> selected={activities[m].index};
            std::vector<int> rest=RecursiveActivitySelector(activities,m,n);
            selected.insert(selected.end(),rest.begin(),rest.end());
            retrurn selected;
        }else{
            // No compatible activities found
            return {};
        }
    }
    ```

  - **Time Complexity**

    - Over all recursive calls, each activity is examined exactly once in the while loop test .
    - Thus the running time of the call `Recursive-ActivitySelector(s,f,0,n+1)` is \( \Theta(n) \) (assuming that the activities have already been sorted by finish times).

- **Implementation**

    ```cpp
    std::vector<int> GreedyActivitySelector(std::vector<Activity>& activities) {
        int n=activities.size();
        std::vector<int> selected;

        selected.push_back(activities[0].index);
        int last_selected=0;

        for(int m=1;m<n;m++){
            if(activities[m].start>=activities[last_selected].finish){
                selected.push_back(activities[m].index);
                last_selected=m;
            })
        }

        return selected;
    }
    ```

  - **Note**
    By following the steps of Greedy-Activity-Selector, we know that its result set A is the same with that of Recursive-ActivitySelector.

## ELements of Greedy Strategy

For a greedy algorithm, each choice seems the best at the moment, but this heuristic strategy does not always produce an optimal solution.

- **develop a greedy algorithm**

    1. **Determine the optimal substructure of the problem.**
    2. **Develop a recursive solution.**
    3. **Prove that at any stage of the recursion, one of the optimal choices is the greedy choice.**  
       Thus, it is always safe to make the greedy choice.
    4. **Show that all but one of the subproblems induced by having made the greedy choice are empty.**
    5. **Develop a recursive algorithm that implements the greedy strategy.**
    6. **Convert the recursive algorithm to an iterative algorithm.**

- **