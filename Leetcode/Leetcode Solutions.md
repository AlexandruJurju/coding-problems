# 1. Two Sum
## Difficulty: Easy
## Topics: Hash table, Hash map
#### Method 1 - Dictionary / HashMap
- Calculate a complement (current - target) value for each number in the array. Use a dictonary to store the complements and the ideces that have been found
- In each pass check if complement of the current value can be found in the dict
```csharp
public int[] TwoSum(int[] nums, int target)
{
	var dictionary = new Dictionary<int, int>();

	for (int i = 0; i < nums.Length; i++)
	{
		// calculate the complement, the difference required to make up the target
		var complement = target - nums[i];

		// if the value has already been seen then 
		if (dictionary.TryGetValue(complement, out var index) && index != i)
		{
			// return current index and the index of the complement
			return [i, index];
		}

		// if the complement hasn't been found in the dict then add the current complement to the dictionary
		dictionary.TryAdd(nums[i], i);
	}

	return [0];
}
```
---


# 2. Add two numbers
## Diff: Medium
## Topics: Linked List
#### Method 1 -
at the start make a new ListNode called result and use a dummyNode
while l1 is not empty and l2 not empty and carry is not empty do
if l1 is not null increment sum with the value and go to next node
if l2 is not null increment sum with the value and go to next node
add carry to the current sum, sum = l1.val + l2.val + carry
the value of the node will be sum % 10 to get the value in range 0,9
the value of carry will be sum / 10
make a new node with the value = sum%10 and

```
public static ListNode addTwoNumbers(ListNode l1, ListNode l2) {
	int carry = 0;
	ListNode dummyHead = new ListNode();
	ListNode result = dummyHead;

	while (l1 != null || l2 != null || carry == 1) {
		int sum = 0;

		if (l1.val != null) {
			sum += l1.val;
			l1 = l1.next;
		}

		if (l2.val != null) {
			sum += l2.val;
			l2 = l2.next;
		}

		sum += carry;
		carry = sum / 10;
		int val = sum % 10;

		ListNode node = new ListNode(val);
		result.next = node;
		result = result.next;
	}

	return dummyHead.next;
}
```

# 3. Longest Substring Without Repeating Characters
##
### Java
#### Method 1 - use 2 pointers to move in the array and a hashset to store the seen characters
When you see a character that isn't in the hashset add it and increase right index, recalculate max with current hashset size. When you see a character that is in the hashset, remove it, increase left pointer

```
public static int lengthOfLongestSubstring(String s) {
	HashSet<Character> hashSet = new HashSet<>();
	int i = 0;
	int j = 0;
	int max = 0;

	while (j < s.length()) {
		if (hashSet.contains(s.charAt(j))) {
			hashSet.remove(s.charAt(i));
			i++;
		} else {
			hashSet.add(s.charAt(j));
			j++;
			max = Math.max(max, hashSet.size());
		}
	}
	return max;
}
```

##### METHOD 2 _ NOT DONE:
```
    public int lengthOfLongestSubstring(String s) {
        HashSet<Character> window = new HashSet<>();
        int index = 0;
        int max = 0;

        while (index < s.length()) {
            if (window.contains(s.charAt(index))) {
                window.remove(s.charAt(index - window.size()));
            }
            window.add(s.charAt(index));
            max = Math.max(max, window.size());
            index++;
        }
        return max;
    }
```

# 11. Container With Most Water
## Diff: Medium | Topics: Two Pointers, Greedy
### Java
#### Method 1 - Use 2 pointers to find area of rectangle
Using a left and right pointer find the area of the rectangle formed by the heights
the floor is right - left, the distance between the indexes
the wall size is the minimum between the 2 walls (if its the max water can fall out of our rectangle :) )
```
public int maxArea(int[] height) {
	int left = 0;
	int right = height.length - 1;

	int maxArea = 0;
	while (left < right) {
		// calculate the area of the rectangle formed by the 2 heights
		// right - left is the floor and Math.min find the minimum wall length (water falls out if we choose the bigger wall from them :) )
		int currentArea = (right - left) * (Math.min(height[left], height[right]));
		maxArea = Math.max(maxArea, currentArea);

		// continue the search to find a better wall
		if (height[left] < height[right]) {
			left++;
		} else {
			right--;	
		}
	}
	return maxArea;
}
```

# 13. Roman to Integer
## Difficulty: Easy
## Topics: Hash table, Math, String
###
- The value of some numerals change based on the next highest value
- To solve this compare the current value to the next one
  - If the current value is bigger than the next value then current value is added
  - If the current value is smaller than the next value then current value is subtracted

```csharp
public int RomanToInt(string s)
{
	var sum = 0;
	var chars = s.ToCharArray();

	for (int i = 0; i < chars.Length - 1; i++)
	{
		var currentValue = GetNumeralValue(chars[i]);
		var nextValue = GetNumeralValue(chars[i + 1]);

		sum += (currentValue < nextValue ? -1 : 1) * currentValue;
	}

	sum += GetNumeralValue(chars[chars.Length - 1]);

	return sum;
}

public int GetNumeralValue(char c)
{
	switch (c)
	{
		case 'I': return 1;
		case 'V': return 5;
		case 'X': return 10;
		case 'L': return 50;
		case 'C': return 100;
		case 'D': return 500;
		case 'M': return 1000;
		default: return 0;
	}
}
```

# 15. 3Sum
## Dif: Medium | Topics: Two Pointers, Binary Search
### Java
#### Method 1 - Two Pointers
Similar to #167 TwoSum when array is sorted, but the third value doesnt change,
Use 2 loops, first one sets the pole value and the second loop is the TwoSum solution with left = poleIndex+1 and right = array.Length-1
```
public List<List<Integer>> threeSum(int[] nums) {
	// needs to be sorted to work, like TwoSum sorted #167
	Arrays.sort(nums);

	// use set to remember DISTINCT solutions
	Set<List<Integer>> intermediaryResults = new HashSet<>();

	int target = 0;

	for (int pole = 0; pole < nums.length; pole++) {
		int left = pole + 1;
		int right = nums.length - 1;
		while (left < right) {
			int sum = nums[pole] + nums[left] + nums[right];
			if (sum == target) {
				intermediaryResults.add(Arrays.asList(nums[pole], nums[left], nums[right]));
				left++;
				right--;
			}

			if (sum > target) {
				right--;
			}

			if (sum < target) {
				left++;
			}
		}
	}
	return new ArrayList<>(intermediaryResults);
}
```

#### Method 2 - Two Pointers Enchanced https://www.nileshblog.tech/leet-code-three-3-sum-java-cpp-python-solution/#Java_Two_Pointer_Approach


# 19. Remove Nth Node From End of List
## Diff: Medium | Topics: Linked List, Two Pointers, Fast and Slow pointers
### Java
#### Method 1 - Fast and Slow pointers https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/
- Move one pointer fast --> n+1 places forward, to maintain a gap of n between the two pointers and then move both at the same speed. 
- Finally, when the fast pointer reaches the end, the slow pointer will be n+1 places behind - just the right spot for it to be able to reached the node to be removed.
- Removal just make the pointer of the prev node point to the one after the target

```
public ListNode removeNthFromEnd(ListNode head, int n) {
	ListNode dummyHead = new ListNode();
	ListNode slow = dummyHead;
	ListNode fast = dummyHead;
	dummyHead.next = head;

	// init the gap between slow and fast pointers
	for (int i = 0; i <= n; i++) {
		fast = fast.next;
	}

	// advance both pointers, keep the gap, until fast reaches end
	while (fast != null) {
		slow = slow.next;
		fast = fast.next;
	}

	// now slow is the previous node before target
	// change previous node pointer to the one after target
	slow.next = slow.next.next;
	return dummyHead.next;
}
```

# 20. Valid Parentheses
## Difficulty: Easy  
## Topics: Stack, String  
### Approach
**Loop over all characters in the input string:**
   - If the character is an opening parenthesis (`(`, `[`, `{`), push it onto the stack.
   - If the character is a closing parenthesis (`)`, `]`, `}`), pop the top element from the stack and check if it matches the corresponding opening parenthesis.
   - If no element can be popped (stack is empty), the parentheses are not valid → return `false`.

**Edge cases:**
   - If the input string length is odd, there will always be an unpaired parenthesis → return `false`.
   - If the stack is not empty after processing all characters, there are unpaired parentheses → return `false`.
---

### Solution Code
```csharp
public bool IsValid(string s)
{
    // If the string length is odd, it cannot be valid
    if (s.Length % 2 == 1) return false;

    Stack<char> parenthesesStack = new Stack<char>();

    foreach (char c in s)
    {
        if (c == '(' || c == '[' || c == '{')
        {
            // Push opening parentheses onto the stack
            parenthesesStack.Push(c);
        }
        else
        {
            // If the stack is empty, no matching opening parenthesis
            if (!parenthesesStack.TryPop(out var popped))
            {
                return false;
            }

            // Check if the popped parenthesis matches the current closing one
            if ((c == ')' && popped != '(') || 
                (c == ']' && popped != '[') || 
                (c == '}' && popped != '{'))
            {
                return false;
            }
        }
    }

    // If the stack is not empty, some parentheses are unpaired
    return parenthesesStack.Count == 0;
}
```

# 21. Merge Two Sorted Lists
## Diff: Easy | Topics: Linked List
### Java
#### Method 1 - Iterate both lists, add min element to new list
- Make a dummy list where the elements are added.
- Loop over each element of the lists until one reaches null.
- For each element compare them and add the minimum to dummy. 
- At the end check the list  that still has elements and add it to the dummy list

```
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
	// if list1 is null then the merged list is just list 2	
	if (list1 == null) {
		return list2;
	}

	// same as above
	if (list2 == null) {
		return list1;
	}

	// make a new list and init a dummy node
	ListNode newHead = new ListNode();
	ListNode dummy = newHead;

	// loop over all elements in the list1 and list2 until the end is reached for one of them
	while (list1 != null && list2 != null) {

		// get the values of the nodes
		int val1 = list1.val;
		int val2 = list2.val;

		// put the next value of dummy the node with the lowest values
		if (val1 <= val2) {
			dummy.next = list1;
			list1 = list1.next;
		} else {
			dummy.next = list2;
			list2 = list2.next;
		}
		// move dummy to next element to prepare for the next list node
		dummy = dummy.next;
	}

	// check to see which node still has elements, and update the ending of the dummy with the non-empty list
	if (list1 != null) {
		dummy.next = list1;
	}
	if (list2 != null) {
		dummy.next = list2;
	}

	return newHead.next;
}
```


# 22. Generate Parentheses 
## Diff: Medium | Topics: Backtracking | Date: ?
### Java
#### Method 1 - Use backtracking
https://leetcode.com/problems/generate-parentheses/solutions/3512769/c-java-python-javascript-using-recursion-with-explanation/
We define a helper function, generateParentheses, that takes the following parameters:
	result: a reference to the vector of strings where we store the generated combinations.
	current: the current combination being generated.
	open: the count of opening parentheses "(" included in the current combination.
	close: the count of closing parentheses ")" included in the current combination.
	n: the total number of pairs of parentheses to be included.

In the generateParentheses function, we first check if the length of the current string is equal to 2n. If it is, we have generated a valid combination, so we add it to the result vector and return.

If the length of current is not equal to 2n, we have two choices:
	If the count of opening parentheses open is less than n, we can add an opening parenthesis to the current combination and make a recursive call to generateParentheses, incrementing the open count by 1.
	If the count of closing parentheses close is less than the open count, we can add a closing parenthesis to the current combination and make a recursive call to generateParentheses, incrementing the close count by 1.

In the generateParenthesis function, we initialize an empty result vector and call the generateParentheses function with the initial values of current as an empty string, open and close counts as 0, and n as the input value.

Finally, we return the result vector containing all the generated combinations of well-formed parentheses.

``` csharp
public List<String> generateParenthesis(int n) {
	List<String> res = new ArrayList<String>();
	backtrack(res, "", 0, 0, n);
	return res;
}

private void backtrack(List<String> results, String current, int open, int closed, int max) {
	if (current.length() == max * 2) {
		results.add(current);
		return;
	}

	if (open < max) {
		backtrack(results, current + "(", open + 1, closed, max);
	}

	if (closed < open) {
		backtrack(results, current + ")", open, closed + 1, max);
	}

}
```


# 26. Remove Duplicates from Sorted Array
##
### Java
https://leetcode.com/problems/remove-duplicates-from-sorted-array/solutions/3676877/best-method-100-c-java-python-beginner-friendly/
The Intuition is to use two pointers, i and j, to iterate through the array. The variable j is used to keep track of the current index where a unique element should be placed. The initial value of j is 1 since the first element in the array is always unique and doesn't need to be changed.


```
public int removeDuplicates(int[] nums) {
	int j = 1;
	for (int i = 1; i < nums.length; i++) {
		// if you find a unique element
		if (nums[i] != nums[i - 1]) {
			// update nums[j] with the value of the unique element at nums[i] and increment j to mark the next position for a new unique element
			nums[j] = nums[i];
			j++;
		}
	}
	return j;
}
```

# 27. Remove Element
## Diff: Easy | Topics: Array, Two Pointers | Date: 25/10/2023
### Java
#### Method 1 - use an index to keep track of the numbers of val values AND as a pointer to the current element in nums to be replaced

```
public static int removeElement(int[] nums, int val) {
	int index = 0;

	for (int i = 0; i < nums.length; i++) {
		if (nums[i] != val) {
			nums[index] = nums[i];
			index++;
		}
	}

	return index;
}
```




# 33. Search in Rotated Sorted Array
## Diff: Medium | Topics: Binary Search, Array
### Java
#### Method 1
https://leetcode.com/problems/search-in-rotated-sorted-array/solutions/3879263/100-binary-search-easy-video-o-log-n-optimal-solution

At any point during the search in the rotated array, one half (either the left or the right) will always be sorted

```
public int search(int[] nums, int target) {
	int left = 0;
	int right = nums.length - 1;

	while (left <= right) {
		int middle = (left + right) / 2;
		int middleElement = nums[middle];

		if (target == middleElement) {
			return middleElement;
		}

		// if the left part is sorted
		if (nums[left] <= nums[middle]) {
			if (nums[left] <= target && target < nums[middle]) {
				right = middle - 1;
			} else {
				left = middle + 1;
			}
		}
		// if the right part is sorted
		else {
			if (nums[middle] < target && target <= nums[right]) {
				left = middle + 1;
			} else {
				right = middle - 1;
			}
		}
	}

	return -1;
}
```

# 36. Valid Sudoku
## Diff: Medium | Topics: Array, Hash Table, 
### Java
####
use a hashset to store all the seen values in the sudoku board
the square elements can be obtained by dividing row and col numbers by 3
1, 2, 3 -> box 1; 4,5,6 -> box 2; 7,8,9 -> box 3

```
public boolean isValidSudoku(char[][] board) {
	HashSet<String> seen = new HashSet<>();

	for (int i = 0; i < board.length; i++) {
		for (int j = 0; j < board[0].length; j++) {
			char elem = board[i][j];
			if (elem != '.') {
				if (!seen.add(elem + "r" + i) || !seen.add(elem + "c" + j) || !seen.add(elem + "b" + i / 3 + j / 3)) {
					return false;
				}
			}
		}
	}
	return true;
}
```

# 39. Combination Sum
## 
### Java
####
```
public List<List<Integer>> combinationSum(int[] candidates, int target) {
	List<List<Integer>> result = new ArrayList<>();
	backtrack(result, new ArrayList<>(), candidates, target, 0);
	return result;
}

public void backtrack(List<List<Integer>> result, List<Integer> temp, int[] candidates, int remainder, int index) {
	if (remainder == 0) {
		result.add(new ArrayList<>(temp));
	}

	if (remainder < 0) {
		return;
	}

	for (int i = index; i < candidates.length; i++) {
		temp.add(candidates[i]);
		// not i+1 because same candidate can be reused
		backtrack(result, temp, candidates, remainder - candidates[i], i);
		temp.remove(temp.size() - 1);
	}
}
```

# 46. Permutations
##
### Java
####
```
public static List<List<Integer>> permute(int[] nums) {
	List<List<Integer>> solution = new ArrayList<>();
	backtrack(solution, new ArrayList<>(), 0, nums);
	return solution;
}

public static void backtrack(List<List<Integer>> solution, List<Integer> temp, int index, int[] nums) {
	if (temp.size() == nums.length) {
		solution.add(new ArrayList<>(temp));
	}

	for (int i = index; i < nums.length; i++) {
		if (temp.contains(nums[i])) {
			continue;
		}
		temp.add(nums[i]);
		backtrack(solution, temp, index, nums);
		temp.remove(temp.size() - 1);
	}
}
```

# 48. Rotate Image
##
###
#### Method 1 - Use ??? theorem
clockwise rotate
first reverse up to down, then swap the symmetry
1 2 3     7 8 9     7 4 1
4 5 6  => 4 5 6  => 8 5 2
7 8 9     1 2 3     9 6 3

anticlockwise rotate
first reverse left to right, then swap the symmetry
1 2 3     3 2 1     3 6 9
4 5 6  => 6 5 4  => 2 5 8
7 8 9     9 8 7     1 4 7


```
public static int[][] transposeMatrix(int[][] matrix) {
	int rows = matrix.length;
	int cols = matrix[0].length;

	for (int i = 0; i < rows - 1; i++) {
		for (int j = i + 1; j < cols; j++) {
			int temp = matrix[i][j];
			matrix[i][j] = matrix[j][i];
			matrix[j][i] = temp;
		}
	}

	return matrix;
}

public static int[][] reverseVertical(int[][] matrix) {
	int rows = matrix.length;
	int cols = matrix[0].length;

	for (int i = 0; i < rows; i++) {
		for (int j = 0; j < cols / 2; j++) {
			int temp = matrix[i][j];
			matrix[i][j] = matrix[i][cols - 1 - j];
			matrix[i][cols - 1 - j] = temp;
		}
	}

	return matrix;
}

public static int[][] rotate90DegreesClockwise(int[][] matrix) {
	return reverseVertical(transposeMatrix(matrix));
}
```

# 49. Group Anagrams
##
### Java
#### Method 1 -
Use a hashmap that contains the sorted string as a key, and a list of all words that have the sorted characters as a value
For each word in strs: sort the characters, check if hashmap contains the sorted word, if not then init a blank ArrayList, at the end add the word to the ArrayList using the key
```
public List<List<String>> groupAnagrams(String[] strs) {
	HashMap<String, List<String>> anagramMap = new HashMap<>();
	for (int i = 0; i < strs.length; i++) {
		char[] strChars = strs[i].toCharArray();
		Arrays.sort(strChars);

		// tried using char[] as key, but the map says it cannot find the key
		String sortedWord = new String(strChars);

		// if hashmap doesn't have the anagram, then add it with an empty list
		if (!anagramMap.containsKey(sortedWord)) {
			anagramMap.put(sortedWord, new ArrayList<>());
		}

		// get the list of the anagram and add the word to it
		anagramMap.get(sortedWord).add(strs[i]);
	}
	return new ArrayList<>(anagramMap.values());
}
```


# 61. Rotate List
## Diff: Medium | Topics: Linked List, Two Pointers | Date: Date: 17/10/2023
### Java
#### Method 1 - https://www.youtube.com/watch?v=UcGtPs2LE_c
public static ListNode rotateRight(ListNode head, int k) {
	// if head is null return null
	if (head == null) {
		return null;
	}

	// find length and tail node of the list
	int length = 1;
	ListNode tail = head;
	while (tail.next != null) {
		tail = tail.next;
		length++;
	}

	// if number of rotates is multiple of k then the list stays the same, use k%length to find the number of real rotates
	k = k % length;

	// if k == 0 then number of rotates is divisible by k, so the resulting list would be the same
	if (k == 0) {
		return head;
	}

	// find the cutoff position in the linked list, the kth node from the tail of the list
	ListNode current = head;
	for (int i = 0; i < length - k - 1; i++) {
		current = current.next;
	}

	ListNode newHead = current.next;
	current.next = null;
	tail.next = head;

	return newHead;
}

# 74.
## Diff: Medium | Binary Search, Matrix
### Java
#### Method 1 -
The main tip is that the rows of the matrix can be seen as a sorted vector. For each row check if target is between those values, if yes then loop over all columns in that row to find the target. If not, then jump to the next row in the matrix

```
public boolean searchMatrix(int[][] matrix, int target) {
	int colIndex = 0;
	int rowIndex = 0;
	int rowLength = matrix.length;
	int colLength = matrix[0].length;

	// if target is smaller than the smallest values and bigger than the greatest then it's not in the matrix
	if (target < matrix[0][0] || target > matrix[rowLength - 1][colLength - 1]) {
		return false;
	}

	while (rowIndex < matrix.length) {
		// check if target could be in sorted vector
		if (target >= matrix[rowIndex][0] && target <= matrix[rowIndex][colLength - 1]) {\
			// if yes, then loop over all columns of that row
			while (colIndex < colLength) {
				if (target == matrix[rowIndex][colIndex]) {
					return true;
				}
				colIndex++;
			}
			// if all columns are checked and the target wasn't found then it cannot be found anywhere else
			return false;
		} else {
			rowIndex++;
		}
	}
	return false;
}
```

# 78. Subsets
## 
### Java
#### Method 1 - backtracking loop from index to num length
```
public static List<List<Integer>> subsets(int[] nums) {
	List<List<Integer>> result = new ArrayList<>();
	List<Integer> current = new ArrayList<>();
	int index = 0;

	backtrack(result, current, index, nums);
	return result;
}

public static void backtrack(List<List<Integer>> results, List<Integer> current, int index, int[] nums) {
	results.add(new ArrayList<>(current));

	for (int i = index; i < nums.length; i++) {
		current.add(nums[i]);
		backtrack(results, current, i + 1, nums);
		current.remove(current.size() - 1);
	}
}
```

#### Method 2 - changed backtrack function
```
public static List<List<Integer>> subsets(int[] nums) {
	List<List<Integer>> result = new ArrayList<>();
	List<Integer> current = new ArrayList<>();
	int index = 0;

	backtrack(result, current, index, nums);
	return result;
}

public static void backtrack(List<List<Integer>> results, List<Integer> current, int index, int[] nums) {
	if (index == nums.length) {
		results.add(new ArrayList<>(current));
		return;
	}

	current.add(nums[index]);
	backtrack(results, current, index + 1, nums);

	current.remove(current.size() - 1);
	backtrack(results, current, index + 1, nums);
}
```

# 94. Binary Tree Inorder Traversal
## Tags: Tree, DFS, Binary Tree
### Java
#### Method 1. Recursive with another function
Inorder traversal, look at Left Node -> Root -> Right Node
```
public List<Integer> inorderTraversal(TreeNode root) {
	List<Integer> list = new ArrayList<Integer>();
	inorder(root, list);
	return list;
}

public void inorder(TreeNode root, List<Integer> nums) {
	if (root == null) return;
	inorder(root.left, nums);
	nums.add(root.val);
	inorder(root.right, nums);
}
```

#### Method 2. Iterative method
```
public List<Integer> inorderTraversal(TreeNode root) {
	List<Integer> list = new ArrayList<Integer>();
	Stack<TreeNode> stack = new Stack<TreeNode>();
	TreeNode cur = root;

	while (cur != null || !stack.empty()) {
		while (cur != null) {
			stack.add(cur);
			cur = cur.left;
		}
		cur = stack.pop();
		list.add(cur.val);
		cur = cur.right;
	}

	return list;
}
```

# 100. Is Same Tree
## Diff: Easy 
## Tags: Tree, Binary Tree, DFS, BFS
### Java
#### Method 1 - Recursive method
https://leetcode.com/problems/same-tree/solutions/3746149/recursive-approach-with-easy-steps/
```
public boolean isSameTree(TreeNode p, TreeNode q) {
	if (p != null && q != null) {
		return p.val == q.val && isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
	} else {
		return p == q;
	}
}
```

```
public boolean isSameTree(TreeNode p, TreeNode q) {
	if (p == null && q == null) // Same Tree
		return true;
	if (p == null || q == null) // Different Size
		return false;
	if (p.val != q.val) // Different Nodes
		return false;
	return isSameTree(p.left, q.left) && // check left subtree
			isSameTree(p.right, q.right); // check right subtree
}
```

# 104. Maximum Depth of Binary Tree
## Diff: Easy
## Tags: 
### Java
#### Method 1
public int maxDepth(TreeNode root) {
	if (root == null) {
		return 0;
	}

	return Math.max(maxDepth(root.left) + 1, maxDepth(root.right) + 1);
}


# 108. Convert Sorted Array to Binary Search Tree
## Diff: Easy | Topics: Binary Search Tree, Tree, Binary Tree, Divide and Conquer | Date: 04/02/2023
### Java
#### Method 1 - Binary Search
Nums is a sorted list, so the middle element nums[mid] is the root of the BST
The next elements of the tree are found by taking the middle elements of the subarrays

public TreeNode sortedArrayToBST(int[] nums) {
	return toBST(nums, 0, nums.length - 1);
}

public TreeNode toBST(int nums[], int left, int right) {
	if (left > right) {
		return null;
	}

	int mid = (left + right) / 2;
	TreeNode root = new TreeNode(nums[mid]);
	root.left = toBST(nums, left, mid - 1);
	root.right = toBST(nums, mid + 1, right);
	return root;
}

# 110. Balanced Binary Tree
##
### Java
####
```
public int height(TreeNode root) {
	if (root == null) {
		return 0;
	}

	return Math.max(height(root.left) + 1, height(root.right) + 1);
}

public boolean isBalanced(TreeNode root) {
	if (root == null) {
		return true;
	}
	if (Math.abs(height(root.left) - height(root.right)) > 1) {
		return false;
	} else {
		return isBalanced(root.left) && isBalanced(root.right);
	}
}
```

# 121. Best Time to Buy and Sell Stock
## Difficulty: Easy
## Topics: Dynamic Programming, Two pointers, Arrays
#### Method 1 - Two Pointers
- use two pointers to represent the buy and sell indeces
- `left` pointer is the buy index and `right` pointer is the sell index 
- in every iteration check the prices
  - if `prices[left]` is smaller than `prices[right]` it means that there is a profit by selling -> check if the profit is bigger than the global profit
  - if `prices[right]` is smaller than `prices[left]` it means that a better buying day has been found, update `left` with the new buy index
  - at the end increase `right`
  
```csharp
public int MaxProfit(int[] prices)
{
	int left = 0;
	int right = 1;
	int maxProfit = 0;

	while (right < prices.Length)
	{
		// if the trade is profitable check if the profit is bigger than the biggest possible profit
		if (prices[left] < prices[right])
		{
			int profit = prices[right] - prices[left];
			if (profit > maxProfit)
			{
				maxProfit = profit;
			}
		}
		// if the trade isn't profitable, right value is bigger than left value, then update left value to be the right value
		else
		{
			left = right;
		}

		// go to the next value
		right++;
	}

	return maxProfit;
}
```

# 125. Valid Palindrome
## Difficulty: Easy
## Topics: Two Pointers, String
#### Method 1 - Two Pointers
- Use to pointers to store the current chars
- Left pointer starts at index 0 and Right pointers starts at the last char
- Each iteration move left pointer to the right and right pointer to the left, then check if the chars are different

```csharp
public bool IsPalindrome(string s)
{
	if (String.IsNullOrEmpty(s)) return true;

	int leftIndex = 0;
	int rightIndex = s.Length - 1;

	while (leftIndex < rightIndex)
	{
		// increment left index until a letter or char is found
		while (leftIndex < rightIndex && !Char.IsLetterOrDigit(s[leftIndex])) leftIndex++;

		// increment right index until a letter or char is found
		while (leftIndex < rightIndex && !Char.IsLetterOrDigit(s[rightIndex])) rightIndex--;
		
		// if the chars are different then return false
		if (char.ToLower(s[leftIndex]) != char.ToLower(s[rightIndex])) return false;
		
		// move the pointers to their next positions
		leftIndex++;
		rightIndex--;
	}

	return true;
}
```
- The input can be cleaned before processing for simpler logic, but worse performance

```csharp
public bool IsPalindrome(string s)
{
	if (String.IsNullOrEmpty(s)) return true;

	var chars = s.ToLower().Where(char.IsLetterOrDigit).ToArray();

	int leftIndex = 0;
	int rightIndex = chars.Length - 1;

	while (leftIndex < rightIndex)
	{
		// if the chars are different then return false
		if (char.ToLower(chars[leftIndex]) != char.ToLower(chars[rightIndex])) return false;

		// move the pointers to their next positions
		leftIndex++;
		rightIndex--;
	}

	return true;
}
```

# 128. Longest Consecutive Sequence
## Diff: Medium | Topics: Array, Hash Table, Union Find
### Java
#### Method 1 - Use a hashset
Use a hashset that contains all the values inside the array. Loop over all values in array and search the hashset to find all values in an incrementing sequence and all values in a decrementing sequence from the current value, for each value in sequence increment the max;

```
public int longestConsecutive(int[] nums) {
	HashSet<Integer> numSet = new HashSet<>();

	// init HasSet with array values
	for (int num : nums) {
		numSet.add(num);
	}

	int longest = 0;
	for (int current : nums) {
		int next = current + 1;
		int prev = current - 1;
		int max = 1;

		// loop over values that are smaller than the current one in order
		while (numSet.contains(prev)) {
			numSet.remove(prev--);
			max++;
		}

		// loop over values that are bigger than the current one in order
		while (numSet.contains(next)) {
			numSet.remove(next++);
			max++;
		}

		longest = Math.max(longest, max);
	}
	return longest;
}
```

# 141. Linked List Cycle
## Diff: Easy | Topics: Linked List, Hash Table, Two Pointers, Fast and Slow pointers
### Java
#### Method 1 - HashSet
Loop over all nodes in the list and use a HashSet to remember the nodes that were seen before
```
public boolean hasCycle(ListNode head) {
	// create the hash set that contains the seen nodes
	HashSet<ListNode> seen = new HashSet<>();
	while (head != null) {
		// if hashset contains has already seen the node then the list has a cycle
		if (seen.contains(head)) {
			return true;
		} else {
			seen.add(head);
		}
		head = head.next;
	}
	return false;
}
```

#### Method 2 - Two-Pointer Method / Floyd's Cycle-Finding Algorithm / Rabbit and Hare
https://leetcode.com/problems/linked-list-cycle/solutions/3999014/99-68-two-pointer-hash-table/

Also known as the "hare and tortoise" algorithm, this method uses two pointers that traverse the list at different speeds. The slow pointer moves one step at a time, while the fast pointer moves two steps. If there is a cycle, the fast pointer will eventually catch up to the slow pointer.

```
public boolean hasCycle(ListNode head) {
	ListNode slow_pointer = head;
	ListNode fast_pointer = head;
	// while end of the list wasnt reached, null cannot be reached if list has a cycle
	while (fast_pointer != null && fast_pointer.next != null) {
		// slow_pointer moves one ahead AND fast_pointer moves 2 ahead
		// eventually if there is a cycle they will meet at some
		slow_pointer = slow_pointer.next;
		fast_pointer = fast_pointer.next.next;
		if (slow_pointer == fast_pointer) {
			return true;
		}
	}
	return false;
}
```

# 150. Evaluate Reverse Polish Notation
##
### Java
#### Method 1 - Stack
If it's a number add it to the stack, if it's an operator pop 2 values, apply operator, push result to the stack
```
public static int evalRPN(String[] tokens) {
	Stack<Integer> stack = new Stack<>();
	for (String token : tokens) {
		switch (token) {
			case "+" -> {
				int val1 = stack.pop();
				int val2 = stack.pop();
				stack.push(val2 + val1);
			}
			case "-" -> {
				int val1 = stack.pop();
				int val2 = stack.pop();
				stack.push(val2 - val1);
			}
			case "*" -> {
				int val1 = stack.pop();
				int val2 = stack.pop();
				stack.push(val2 * val1);
			}

			case "/" -> {
				int val1 = stack.pop();
				int val2 = stack.pop();
				stack.push(val2 / val1);
			}

			default -> stack.push(Integer.valueOf(token));
		}
	}
	return stack.pop();
}
```

# 153. Find Minimum in Rotated Sorted Array
## Diff: Med | Topics: Binary Search | Date: ?
### Java
#### Method 1
You can compare the middle element with the first and last elements to find where the sorted array start IE where the minimum element is. If middle element > nums[right] that means that the sorted array must start in the right part and continue in the left part. If element < nums[left] it means that the array starts in the left part.

```
public int findMin(int[] nums) {
    int left = 0;
    int right = nums.length - 1;

    while (left <= right) {
        int middle = (left + right) / 2;
        int middleElement = nums[middle];

        if (left == right) {
            return middleElement;
        }

        if (middleElement > nums[right]) {
            left = middle + 1;
        } else {
			// right = middle - 1 is not right because in the next search the middle element is skipped, problem just for this func - right = middle - 1 works for normal binary search
            right = middle;
        }

    }

    return -1;
}
```

# 167. Two Sum II - Input Array Is Sorted
##
###
#### Method 1 - use 2 pointers
use 2 pointers, left and right to choose elements in the sorted array
calculate sum as numbers[left] + numbers[right]
if target > sum it means that sum needs to grow, and since right already points to the biggest possible element, increment left pointer to increase sum value
if target < sum, that means that sum need to be smaller, since left already points to the smallest possible element, decrement right to decrease sum value

```
public int[] twoSum(int[] numbers, int target) {
	int left = 0;
	int right = numbers.length - 1;

	while (left < right) {
		// left points to the leftmost element, left points to a small element
		int leftVal = numbers[left];
		// right points to the rightmost element, right points to a larger element
		int rightVal = numbers[right];

		// calculate the sum as leftValue + rightValue
		int sum = leftVal + rightVal;

		if (sum == target) {
			return new int[]{left + 1, right + 1};
		}

		// compare target to sum to see how to move left and right so that  sum == target
		if (target > sum) {
			left++;
		}

		if (target < sum) {
			right--;
		}
	}

	return null;
}
```

# 189.
## Diff: Medium | Topics: Linked List, Two Pointers
### Java
#### Method 1 - Use 2 pointers

```
// rotate right
public void rotate(int[] nums, int k) {
	// if k is more than the length of the array, then the shifting will return the array to the original values
	k = k % nums.length;

	// if k == 0 then rotations don't change the array
	if (k == 0) {
		return;
	}

	// input : 1,2,3,4,5, k = 2

	// need 3 reversals
	// 1. reverse the whole array
	// 5,4,3,2,1
	rotateRange(nums, 0, nums.length - 1);

	// 2. reverse the first k elements
	// 4,5,3,2,1
	rotateRange(nums, 0, k - 1);

	// 3. reverse the remaining size - k elements
	// 4,5,1,2,3
	rotateRange(nums, k, nums.length - 1);
}

public void rotateRange(int[] nums, int left, int right) {
	while (left < right) {
		int temp = nums[left];
		nums[left] = nums[right];
		nums[right] = temp;
		left++;
		right--;
	}
}
```

```
// rotate left
public void rotate(int[] nums, int k) {
	// if k is more than the length of the array, then the shifting will return the array to the original values
	k = k % nums.length;

	// if k == 0 then rotations don't change the array
	if (k == 0) {
		return;
	}

	// 1. reverse whole array
	rotateRange(nums, 0, nums.length - 1);

	// 2. reverse first size - k elements
	rotateRange(nums, 0, nums.length - 1 - k);

	// 3. reverse remaining k elements
	rotateRange(nums, nums.length - k, nums.length - 1);
}
```

# 200. Number of islands ???
```
public static int numIslands(char[][] grid) {
	int islandCount = 0;
	for (int i = 0; i < grid.length; i++) {
		for (int j = 0; j < grid[0].length; j++) {
			if (grid[i][j] == '1') {
				islandCount++;
				DFSSink(grid, i, j);
			}
		}
	}
	return islandCount;
}

// recursive DFS function
public static void DFSSink(char[][] grid, int i, int j) {
	if (i < 0 || j < 0 || i >= grid.length || j >= grid[0].length || grid[i][j] != '1') {
		return;
	}

	grid[i][j] = '0';
	DFSSink(grid, i + 1, j);
	DFSSink(grid, i - 1, j);
	DFSSink(grid, i, j + 1);
	DFSSink(grid, i, j - 1);
}
```

# 206. Reverse Linked List
## Diff: Easy | Topics: Linked List
### Java
#### Method 1
Simple way to solve this, loop over all nodes and just reverse the pointers to the previous element

```
public ListNode reverseList(ListNode head) {
	// 2 nodes, currentNode of the first list and previousNode of the inverted list
	ListNode currentNode = head;
	ListNode previousNode = null;

	// loop until the last node of the initial list
	while (currentNode != null) {
		// save the next node in the initial list
		ListNode next = currentNode.next;

		// the current node points to the previous element
		currentNode.next = previousNode;

		// update the previous node as the current node
		previousNode = currentNode;

		// move to the next node in the list
		currentNode = next;
	}

	return previousNode;
}
```

# 219. Contains Duplicate II
## Diff: Easy | Topics: Array, Hash Table, Sliding Window
### Java
#### Method 1 - Window is HashSet

The problem is trying to find if there are duplicates inside a window of elements with size k.
Loop over all elements and put them in a hashset. If the size of the hashset is k then remove the oldest element from the hashset. If there are 2 elements with the same value in window -> duplicate

```
public boolean containsNearbyDuplicate(int[] nums, int k) {
	HashSet<Integer> window = new HashSet<>();
	for (int i = 0; i < nums.length; i++) {
		// if there is a duplicate in the window
		if (window.contains(nums[i])) {
			return true;
		}

		window.add(nums[i]);

		// remove last element in window when size is k to simulate the sliding motion
		if (window.size() > k) {
			window.remove(nums[i - k]);
		}
	}
	return false;
}
```

# 226. Invert Binary Tree
##
###
####
```
public static TreeNode invertBinaryTree(TreeNode root) {
	if (root == null) {
		return root;
	}

	invertBinaryTree(root.left);
	invertBinaryTree(root.right);

	// when the 2 recursion calls are done, the root node contains the edge left node and the edge right node
	// swap the nodes before returning root
	TreeNode tmp = root.left;
	root.left = root.right;
	root.right = tmp;

	return root;
}
```

# 238. Product of Array Except Self
## Diff: Medium | Topics: Array, Prefix Sum
### Java
#### Method 1 - https://www.youtube.com/watch?v=bNvIQI2wAjk
For each number calculate a prefix product of all elements before the value, and postfix product of all elements after the value
The product of all values except self is the previous value in prefix and the next value in postfix

```
public static int[] productExceptSelf(int[] nums) {
	int length = nums.length;
	int[] prefix = new int[length], postfix = new int[length], res = new int[length];

	prefix[0] = nums[0];
	postfix[length - 1] = nums[length - 1];

	for (int i = 1; i < length; i++) {
		prefix[i] = prefix[i - 1] * nums[i];
	}

	for (int i = length - 2; i >= 0; i--) {
		postfix[i] = postfix[i + 1] * nums[i];
	}

	for (int i = 0; i < length; i++) {
		int pref = 1;
		int post = 1;
		if (i > 0) {
			pref = prefix[i - 1];
		}
		if (i < length - 1) {
			post = postfix[i + 1];
		}

		res[i] = pref * post;
	}

	return res;
}
```

# 240. Search a 2d Matrix
##
### Java
#### Method 1 - use 2 pointers
start looking in the matrix from the bottom left element and value of current element
bottom left element was chosen because if we go up then there are only smaller values and if we go right there are bigger values
if current element < target then increment col index to find bigger number
if current element > target then increment row index to find lower number

```
public boolean searchMatrix(int[][] matrix, int target) {
	int rows = matrix.length, cols = matrix[0].length;
	int row = matrix.length - 1, col = 0;

	while (row >= 0 && col < cols) {
		int elem = matrix[row][col];

		if (elem == target) {
			return true;
		}

		if (target > elem) {
			col += 1;
		}
		if (target < elem) {
			row -= 1;
		}
	}
	return false;
}
```


# 242. Valid Anagram
### Difficulty: Easy
### Topics: Hash table, String, Sorting
#### Method 1
- Use a dictionary to store the chars and the number of appearences of each char in one string
- Loop over all chars of the second string:
  - If the char cannot be found in the dict then they are not anagrams
  - If the count of the char is different than the first string then they are not anagrams

```
public bool IsAnagram(string s, string t)
{
	if (s.Length != t.Length)
	{
		return false;
	}

	// initialize the dictionary with the chars and the frequency of each char for the first string
	var dict = new Dictionary<char, int>();
	foreach (var c in s)
	{
		if (dict.TryGetValue(c, out int count))
		{
			dict[c] = count + 1;
		}
		else
		{
			dict.Add(c, 1);
		}
	}

	// iterate over all chars of the second string
	foreach (var c in t)
	{
		// if the char cannot be found in the dict or the count is different than the first string -> not anagrams
		if (!dict.TryGetValue(c, out int count) || count == 0)
		{
			return false;
		}

		dict[c] = count - 1;
	}

	return true;
}
```

#### Method 2 - Sort the chars
- Convert the strings int char arrays, sort the arrays and then compare them, if they are different that means that the words cannot be anagrams

```
public bool IsAnagram(string s, string t)
{
	if (s.Length != t.Length) return false;

	var first = s.ToCharArray();
	Array.Sort(first);

	var second = t.ToCharArray();
	Array.Sort(second);


	for (int i = 0; i < first.Length; i++)
	{
		if(first[i] != second[i]) return false;
	}
	
	return true;
}
```


# 268. Missing Number
## Diff: Easy | Topics: Array, Hash Table, Math, Binary Search, Bit Manipulation, Sorting
### Java
#### Method 1 - Difference between expected sum and actual sum | Runtime: 33% Memory: 92%
For this problem, because a single element is missing and all elements are increasing by 1, you can calculate the missing number by calculating the difference between expected sum of the array [N*(N-1)]/2 and the actual sum in the input array

```
public int missingNumber(int[] nums) {
	int n = nums.length;
	int expectedSum = (n * (n + 1)) / 2;
	int realSum = Arrays.stream(nums).sum();
	return expectedSum - realSum;
}
```

#### Method 2 - Sorting the array

# 271. Contains Duplicate
##
### Java
#### Method 1 - Sorting -> the array and check the next element if it equals the current one

```
public boolean containsDuplicate(int[] nums) {
	Arrays.sort(nums);
	for (int i = 0; i < nums.length - 1; i++) {
		if (nums[i] == nums[i + 1]) {
			return true;
		}
	}
	return false;
}
```

#### Method 2 - Hashset
create a hashset then loop over elements in array for each element check if its already in the hashset
if it is then the array contains a duplicate -> return true otherwise add it to the hashset and continue checking
if loop finishes without finding any elements in the hashset then there are no duplicates -> return false

```
public static boolean containsDuplicate(int[] nums) {
	HashSet<Integer> seen = new HashSet<>();

	for (int num : nums) {
		if (seen.contains(num)) {
			return true;
		}
		seen.add(num);
	}

	return false;
}
```

# 347. Top K Frequent Elements
##
### Java
#### Method 1 - Use a maxheap
Use a hashmap with <Key=NumValue,Value=Frequency> to find the frequency of the elements in the array. Then use a maxheap to get the max element, the element with the hightest frequency from the map

```
public int[] topKFrequent(int[] nums, int k) {
	HashMap<Integer, Integer> frequencyMap = new HashMap<>();
	for (int elem : nums) {
		// increment value in map if key is present, otherwise use 0 as default value to increment
		frequencyMap.put(elem, frequencyMap.getOrDefault(elem, 0) + 1);
	}

	// define the priority of the queue, max value
	PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> frequencyMap.get(b) - frequencyMap.get(a));
	maxHeap.addAll(frequencyMap.keySet());

	int[] res = new int[k];
	for (int i = 0; i < k; i++) {
		res[i] = maxHeap.poll();
	}
	return res;
}
```

# 463. Island Perimeter
## Diff: Easy | Topics: DFS, BFS | Date: 29/01/2024

Use BFS or DFS to iterate over all 1s inside of an island
When 1 is encountered increment the counter and replace 1 with another value
Then look at the neighbours

### Java
public int islandPerimeter(int[][] grid) {
	if (grid == null) {
		return 0;
	}

	for (int i = 0; i < grid.length; i++) {
		for (int j = 0; j < grid[0].length; j++) {
			if (grid[i][j] == 1) {
				return calculatePerimeter(grid, i, j);
			}
		}
	}

	return 0;
}

public static int calculatePerimeter(int[][] grid, int i, int j) {
	if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length) {
		return 1;
	}

	if (grid[i][j] == 0) {
		return 1;
	}

	if (grid[i][j] == -1) {
		return 0;
	}

	int count = 0;
	grid[i][j] = -1;
	count += calculatePerimeter(grid, i + 1, j);
	count += calculatePerimeter(grid, i - 1, j);
	count += calculatePerimeter(grid, i, j + 1);
	count += calculatePerimeter(grid, i, j - 1);

	return count;
}

# 509. Fibonacci Number
## Diff: Easy
## Topics: Math, Dynamic Programming, Recursion, Memoization
### Java
#### Method 1 - Recursive

```
public int fib(int n) {
	if (n == 0 || n == 1) {
		return n;
	}

	return fib(n - 1) + fib(n - 2);
}
```

#### Method 2 - Dynamic programming, Bottom Up
Use an array to store previous results of fib(i), reuse the calculated results
```
public int fib(int n) {
	if(n == 0 || n == 1){
		return n;
	}

	int[] fibSequence = new int[n];
	fibSequence[0] = 1;
	fibSequence[1] = 1;

	for (int i = 2; i < n; i++) {
		fibSequence[i] = fibSequence[i - 1] + fibSequence[i - 2];
	}

	return fibSequence[n - 1];
}
```

#### Method 3 - Dynamic programming, Top Down
Start from the highest index
```
int[] fib_cache = new int[31];

public int fib(int N)
{
	if(N <= 1)
		return N;
	else if(fib_cache[N] != 0)
		return fib_cache[N];
	else
		return fib_cache[N] = fib(N - 1) + fib(N - 2);
}
```


# 572. Subtree of Another Tree
##
### Java
#### Method 1 - recursive use isSameTree function
```
public boolean isSameTree(TreeNode p, TreeNode q) {
	if (p == null && q == null) {
		return true;
	}

	if (p == null || q == null || p.val != q.val) {
		return false;
	}

	return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}

public boolean isSubtree(TreeNode root, TreeNode subRoot) {
	if (subRoot == null) {
		return true;
	}

	if (root == null) {
		return false;
	}

	return isSameTree(root, subRoot) || isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot);
}
```

# 682. Baseball Game
## Diff: Easy | Topics: Stack | Date: 25/10/2023
### Java
```
public static int calPoints(String[] operations) {
	Stack<Integer> vals = new Stack<Integer>();
	int total = 0;

	for (String s : operations) {
		switch (s) {
			case "+" -> {
				int num1 = vals.pop();
				int res = (vals.peek() + num1);
				vals.push(num1);
				vals.push(res);
			}
			case "D" -> vals.push(vals.peek() * 2);
			case "C" -> vals.pop();
			default -> vals.push(Integer.valueOf(s));
		}
	}

	while (!vals.isEmpty()) {
		total += vals.pop();
	}

	return total;
}
```

# 700. Search in a Binary Search Tree
## Diff: Easy
## Topics: Tree, Binary Search Tree
### Java
```
public TreeNode searchBST(TreeNode root, int val) {
	// no answer found
	if (root == null) {
		return null;
	}

	// found value
	if (root.val == val) {
		return root;
	}

	// if target is more than current node value, then search in left subtree
	if (root.val > val) {
		return searchBST(root.left, val);
	}
	// if target is less than current node value, then search in right subtree
	else {
		return searchBST(root.right, val);
	}
}
```

# 704. Binary Search 
## Difficulty: Easy
## Topics: Binary Search, Array
#### Solution
- Use 2 indeces, left and right to create the lookup window
- in each iteration look at the middle of the array and compare the value to the target
  - if the value is bigger than the target then continue looking in the left side of the array
  - if the value is smaller than the target then continue looking in the right side of the array

```csharp
public int Search(int[] nums, int target)
{
	if (nums.Length == 0) return -1;

	int left = 0;
	int right = nums.Length - 1;

	while (left <= right)
	{
		int middle = (left + right) / 2;

		if (nums[middle] == target)
		{
			return middle;
		}

		if (nums[middle] > target)
		{
			right = middle - 1;
		}
		else
		{
			left = middle + 1;
		}
	}

	return -1;
}
```

# 739. Daily Temperatures
## Diff: Medium | Topics: Monotonic Stack, Stack
### Java
#### Method 1 - Monotonic stack
Use a monotonic stack to store the indexes of temeperatures. When a temperature that is higher than the current top is found then pop the elements from the stack and add the indexes to the result
```
public int[] dailyTemperatures(int[] temperatures) {
	Stack<Integer> monotonicStack = new Stack<>();
	int[] result = new int[temperatures.length];

	for (int i = 0; i < temperatures.length; i++) {
		while (!monotonicStack.isEmpty() && temperatures[monotonicStack.peek()] < temperatures[i]) {
			result[monotonicStack.peek()] = i - monotonicStack.pop();
		}
		monotonicStack.push(i);
	}
	return result;
}
```

# 876. Middle of the Linked List
## Diff: Easy
## Topics: Linked List, Two Pointers, Fast and Slow Pointers
### Java
#### Method 1

# 2428. Maximum Sum of an Hourglass
## Diff: Medium | Topics: Array, Matrix, Prefix Sum
### Java
#### Method 1 - Brute force sum elements
Brute force the sum calculations. Take every element and calculate the hourglass sum in the matrix

```
public int maxSum(int[][] grid) {
	int max = Integer.MIN_VALUE;
	// use grid.length - 2 to make sure the last elements that are summed can still form an hourglass
	for (int i = 0; i < grid.length - 2; i++) {
		for (int j = 0; j < grid[0].length - 2; j++) {
			int topRow = grid[i][j] + grid[i][j + 1] + grid[i][j + 2];
			int middleValue = grid[i + 1][j + 1];
			int bottomRow = grid[i + 2][j] + grid[i + 2][j + 1] + grid[i + 2][j + 2];
			int sum = topRow + middleValue + bottomRow;
			max = Math.max(max, sum);
		}
	}
	return max;
}
```

TODO
#### Method 2 - Prefix Sum 

# Isogram
public static boolean isIsogram(String input) {

    input = input.toLowerCase();
    HashSet<Character> characters = new HashSet<>();

    for (char c : input.toCharArray()) {
        if (characters.contains(c)) {
            return false;
        } else {
            characters.add(c);
        }
    }

    return true;
}