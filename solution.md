# Intuition
<!-- Describe your first thoughts on how to solve this problem. -->
This problem requires us to sum `3` different values in an array to find which combinations are equal to our target value of `0`. Right off the bat, this is fairly similar to the sum `2` values in an array to find `x` which is a simple two pointer problem.
# Approach
<!-- Describe your approach to solving the problem. -->
We are going to approach this problem utilizing the two pointers algorithm. However, we do have a third variable to account for so our approach will be a slight variation on the two pointer approach.
1. Setup
Given an array of integers, `nums`, of size `n`, we need to find three values,`nums[i], nums[j], nums[k]`which sum to `0`. We can't use the same index more than once so `i != j != k`. 
2. Two pointer algorithm
The two pointer algorithm compares elements pointed to by two different pointers in a sorted array, list, etc. and updates one of the pointer depending on these elements compare. 
3. Utilize a sorted array to account for duplicates values
Because our values are in ascending order after we sort them, all duplicate values will be placed next to each other and we can simply skip over them to avoid inserting identical entries in our output
4. Introduce third pointer as the range for Two Pointer algorithm
We can treat our third pointer as a binding range for `nums` for which we perform our Two pointer algorithm. This third pointer will begin at `n-1` decrement by `1` until we reach `0`.

## Consider a 2sum problem
**You can skip this section if you feel you understand how the two pointer algorithm works.**

Lets simplify the problem first to a 2sum problem instead. In this scenario, we are given an array of integers `nums` we need to find two values such that `nums[i] + nums[j] = 0` and `i != j`.
A brute force method would compare every single value against every other value. Feel free to review LeetCode's explanation of the [Two Pointer Technique](https://leetcode.com/articles/two-pointer-technique/) as well.

**2sum Brute Force algorithm**
```
def bruteForce(nums: List[int]):
    n = len(nums)
    output = []

    for i in range(n): # O(n)
        for j in range(n): # O(n)
            if j == i:
                # i != j for our output
                continue 
            if nums[i] + nums[j] == 0:
                # our output cannot include duplicate values
                if [nums[i], nums[j]] not in output: # ~O(n)
                    output.append([nums[i], nums[j]])

    return output

```
In the example, our final time complexity is $O(n^3)$. Using any data structure that hashes values such as a set or dictionary for our output would reduce the time need to check for duplications from $O(n)$ to $O(1)$, for a total time of $O(n^2)$. However, this introduces other complications into the algorithm that would need to be dealt with in the algorithm.

A two pointer algorithm can take advantage of the fact that we can know how far off from our target value `0` we are and adjust our two input values accordingly. To do this we need to first sort our array in ascending order. This can be done in $~O(n log{}{n})$ time using Python's `.sort()` method, e.g. `[-1, 0, 1, 2, -1, -4] -> [-4, -1, -1, 0, 1, 2]`. Now that `nums` is sorted, we can select the smallest value at `nums[0] = -4` and our largest value `nums[n-1] = 2` and sum them together `nums[0] + nums[n-1] = -2` or `-4 + 2 = -2`. In this case we undershot our target value of `0` by `-2`. To adjust for our summed value being less than our target value, we increment our index/pointer at `0` to the next smallest value at index `1`i.e`nums[1] = -1`. This gives us `nums[1] + nums[n-1] = 1` or `-1 + 2 = 1`. In this case, we overshot or target value of `0` by `1`. Similar to before, we can adjust for this by moving one of our pointers/indices. Since we overshot the target value this time, we will instead decrement our largest value at index `n-1` to our second larget value at `n-2` i.e. `nums[n-2] = 1`. This gives us `nums[1] + nums[n-2] = 0`. This time we got our target value of `0`. We can either move our pointer/idex the next largest value and repeat this process until you are done to find all possible combinations that sum to `0`.

**2sum Two Pointer algorithm**:
```
def twoSum(nums: List[int]):
    n = len(nums)
    output = []

    # Sorts nums values in ascending order
    nums.sort() # O(nlogn)

    i = 0 # index of smallest value
    j = n - 1 # index of largest value

    # Since we are always inc or dec an index with nums, once
    # i => j, we know we've gone through the entire list
    while i < j: # O(n)

        # Undershot and need to inc i to point to the next smallest val
        if nums[i] + nums[j] < 0:
            i += 1

        # Overshot and need to dec j to point to the next largest val
        elif nums[i] + nums[j] > 0:
            j -= 1

        # Summed values = 0 and we can add it to output
        else:

            # Avoids inserting duplicates into output
            if [nums[i], nums[j]] not in output: # ~O(n)
                output.append([nums[i], nums[j]])

            # Moves to the next largest value and repeat!
            j -= 1

    return output
```

In this two pointer solution, our final time complexity is $O(n^2)$ which we get from our sorting of `nums` + our `while` loop * checking for duplicates which equals $O(n log{}{n})$ + $O(n)$ * $O(n)$ = $O(n log{}{n})$ + $O(n^2)$ = $O(n^2)$. 

Checking for duplicates this way is incredibly slow and we can fix this by using a hashable data structure such as a hash set or we can again take advantage of the fact that our list is sorted and duplicate values will be next to each other. To do this, we simply skip over any values that match the previous value.

**2sum Adjusted Two Pointer algorithm to optimized for duplicates:**
```
def twoSum(nums: List[int]):
    n = len(nums)
    output = []
    nums.sort() # O(nlogn)

    i = 0
    j = n - 1 
    while i < j: # O(n)
        if nums[i] + nums[j] < 0:
            i += 0
            # Skip duplicate values for inc small pointer i
            while nums[i] == nums[i-1] and i < j:
                i += 1

        elif nums[i] + nums[j] > 0:
            j -= 1
            # Skip duplicate values for dec large pointer j
            while nums[j] == nums[j+1] and j > i:
                j -= 1
        else:
            output.append([nums[i], nums[j]])
            j -= 1
            # We need to do this anytime we dec j
            while nums[j] == nums[j+1] and j > i:
                j -= 1

    return output
```

This adjustment reduces our `while` loop's time complexity from $O(n^2)$ to $O(n)$. Thus our final time complexity is $O(n log{}{n})$ + $O(n)$ = $O(n log{}{n})$ + $O(n)$ = $O(n log{}{n})$. A faster algorithm can be done $O(n)$ utilizing a hash table based algorithm instead of a two pointer algorithm but good luck figuring that out for our 3sum problem. 

## 3sum Solution

The final step is to modify our two pointer algorithm to include a third pointer to a third element in our summation to reach our target value of `0`. Much like the [Three-body Problem](https://en.wikipedia.org/wiki/Three-body_problem) in physics, movement in any of the three pointers can make it difficult to predict which pointer needs to move in order to account for all possible solutions. The simplest way to mitigate these complexities is to have our third pointer `k` act as the range from `0` to `k` in `nums` to perform our two pointer algorithm on. We initialize `k` to the size of `nums` or simply `n`. We decrement `k` by `1` each iteration until we have gone through all sizes of `nums`. In other words we perform our two pointer algorithm on list `num[0:k]` with `k` getting smaller each time. This allows us to account for all possible combinations in `nums` to reach our target value of `0`. We can access this range of `0` to `k` as a `for` loop starting at `n-1` and decrementing by `1` until we reach `0`. We will place our two pointer algorithm from before inside this `for` loop . Since duplicate values will be adjacent to each other in our sorted list, we can simply skip over any we encounter in our `for` loop for our pointer `k`.

**3sum solution with Two Pointer Algorithm:**
```
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        nums.sort() # O(nlogn)
        output = []
        
        # Loop for pointer/index k which binds the range nums[0:k]
        # to perform two pointer algorithm
        for k in range(n-1, -1, -1): # O(n)
            # Accounts for duplicates nums[k] may encounter
            if k < n - 1 and nums[k+1] == nums[k]:
                continue

            # Two pointer algorithm starts here
            i = 0
            j = k - 1
            while i < j: # O(n)
                if nums[i] + nums[j] + nums[k]> 0:
                    j -= 1
                elif nums[i] + nums[j] + nums[k] < 0:
                    i += 1
                else:
                    output.append([nums[i], nums[j], nums[k]])
                    j -= 1
                    while nums[j+1] == nums[j] and j > i:
                        j -= 1

        return output
```
Faster solutions exist but this approach is easier to grasp as it build off of a simple Two Pointers problem. 
# Complexity
- Time complexity: $O(n^2)$
Sorting of nums + binding range for loop * Two Pointer algorith = $O(n log{}{n})$ + $O(n)$ * $O(n)$ = $O(n log{}{n})$ + $O(n^2)$ = $O(n^2)$. Expect to beat ~40-70% of solutions.
<!-- Add your time complexity here, e.g. $O(n)$ -->


- Space complexity: $O(n)$
Worse case `output` is $O(n)$. All other variables are $O(1)$. Expect to beat about ~90% of solutions.
<!-- Add your space complexity here, e.g. $O(n)$ -->

# Code
```
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        nums.sort()
        output = []

        for k in range(n-1, -1, -1):
            if k < n - 1 and nums[k+1] == nums[k]:
                continue
            i = 0
            j = k - 1
            while i < j:
                if nums[i] + nums[j] + nums[k]> 0:
                    j -= 1
                elif nums[i] + nums[j] + nums[k] < 0:
                    i += 1
                else:
                    output.append([nums[i], nums[j], nums[k]])
                    j -= 1
                    while nums[j+1] == nums[j] and j > i:
                        j -= 1

        return output
```
