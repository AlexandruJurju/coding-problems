

# 3. Longest Substring Without Repeating Characters
## Difficulty: Medium
## Topics: Sliding Window, Hash Map

### Solution 1 - Sliding window and hashset
#### Beats: 68%
- Sliding window and hashset to store seen characters
- While `s[right]` isn't contained in hashSet continue to increment right
- When a duplicate is found remove character `s[left]` from hashSet and increment left until the duplicate is removed from the hashSet
  - This allows the search to continue in a new substring without duplicate characters 
  - Ex: a b c d e, if c is added a,b,c get removed from the hashSet -> the resulting hashSet is d, e, c left is on d and right is on c

```csharp
public int LengthOfLongestSubstring(string s)
{
	// use a set to store the chars that have been seen already
	var seen = new HashSet<char>();
	int maxLength = 0;
	int left = 0;

	for (int right = 0; right < s.Length; right++)
	{
		// if seen doesn't contain s[right] then it's a new unique char
		if (!seen.Contains(s[right]))
		{
			// add it to seen and update maxLength
			seen.Add(s[right]);
			maxLength = Math.Max(maxLength, right - left + 1);
		}
		// if current char has been seen then left pointer needs to be moved until the duplicate is removed from the set
		// after left is moved the duplicate character from right can be added to continue the search in a new substring
		else
		{
			while (seen.Contains(s[right]))
			{
				seen.Remove(s[left]);
				left++;
			}

			seen.Add(s[right]);
		}
	}

	return maxLength;
}
```

# 11. Container With Most Water
## Difficulty: Medium
## Topics: Two Pointers, Greedy

### Solution 1 
- Two pointers, one at the start and one at the end of the array
- In each iteration:
  - calculate current area of the box by picking the smallest of the 2 walls and multiply with the length
  - update the pointers so that the pointer of the smallest wall gets moved to the next position

```csharp
public class Solution
{
    public int MaxArea(int[] height)
    {
        int left = 0;
        int right = height.Length - 1;
        int maxWater = 0;

        while (left < right)
        {
            // The lowest wall gives the limit for the water
            int currentArea = Math.Min(height[left], height[right]) * (right - left);

            maxWater = Math.Max(maxWater, currentArea);

            if (height[left] < height[right])
            {
                left++;
            }
            else
            {
                right--;
            }
        }

        return maxWater;
    }
}
```


# 13. Roman to Integer
## Difficulty: Easy
## Topics: Hash Map, Math, String
###
- The value of some numerals change based on the next highest value
- To solve this compare the current value to the next one
  - If the `current value` is bigger than the next value then current value is added
  - If the `current value` is smaller than the next value then current value is subtracted

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

# 14. Longest Common Prefix
## Difficulty: Easy
## Topics: String, Trie

### Solution 1 - Sort the array
- Sorting the strings and then comparing the first and last elements will make it possible to find the common prefix for all the strings

```csharp
using System.Text;

public class Solution
{
    public string LongestCommonPrefix(string[] strs)
    {
        var sBuilder = new StringBuilder();

        Array.Sort(strs);
        var first = strs[0];
        var second = strs[strs.Length - 1];
		// only look for the mininum string length
        int minLength = Math.Min(first.Length, second.Length);

        for (int i = 0; i < minLength; i++)
        {
            if (first[i] != second[i]) break;
            sBuilder.Append(first[i]);
        }

        return sBuilder.ToString();
    }
}
```

### Solution 2 - Loop over all strings
- Find the length of the smallest string 
- Compare the characters of the smallest word for all the words
- If there are any mismatches return

```csharp
using System.Text;

public class Solution
{
    public string LongestCommonPrefix(string[] strs)
    {
        int minLength = Int32.MaxValue;
        foreach (var str in strs)
        {
            minLength = Math.Min(minLength, str.Length);
        }

        int index = 0;
        while (index < minLength)
        {
            foreach (var s in strs)
            {
                if (s[index] != strs[0][index])
                {
                    return s.Substring(0, index);
                }
            }
            index++;
        }

        return strs[0].Substring(0, index);
    }
}
```



# 15. 3Sum
## Difficulty: Medium
## Topics: Binary Search, Two Pointers, Sorting

### Solution 1 
- Like 2Sum but an element is a pivot and doesn't change

```csharp
using System;
using System.Collections.Generic;

class Solution
{
    public IList<IList<int>> ThreeSum(int[] nums)
    {
        var result = new List<IList<int>>();

        Array.Sort(nums);

        for (int i = 0; i < nums.Length; i++)
        {
            // Skip checking for duplicate values - have been checked in the last iteration
            if (i > 0 && nums[i] == nums[i - 1]) continue;
            
            int target = -nums[i];

            // Start checking from the next element to the end
            int left = i + 1;
            int right = nums.Length - 1;
            while (left < right)
            {
                int currentSum = nums[left] + nums[right];
                if (currentSum == target)
                {
                    result.Add(new List<int>() { nums[i], nums[left], nums[right] });
                    
                    // Skip duplicate left and right elements
                    while (left < right && nums[left] == nums[left + 1]) left++;
                    while (left < right && nums[right] == nums[right - 1]) right--;
                    
                    // Jump to next elements
                    left++;
                    right--;
                }
                // If currentSum < target it means we need a bigger element - because array is sorted incrementing left index will increase the sum 
                else if (currentSum < target)
                {
                    left++;
                }
                // If currentSum > target it means we need a lower element - because array is sorted decrementing right index will lower the sum 
                else
                {
                    right--;
                }
            }
        }

        return result;
    }
}
```

# 20. Valid Parentheses
## Difficulty: Easy  
## Topics: Stack, String  
### Solution 1 - Stack for pairs
- **Loop over all characters in the input string:**
   - If the character is an opening parenthesis (`(`, `[`, `{`), push it onto the stack
   - If the character is a closing parenthesis (`)`, `]`, `}`), pop the top element from the stack and check if it matches the corresponding opening parenthesis, if they dont match → return `false`
   - If no element can be popped (stack is empty), the parentheses are not valid → return `false`
---

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
            // If the stack is empty then there is no matching paranthesis -> false
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

### Solution 2 - Stack optimized
- When encountering an opening paranthesis put the closing one in the stack
- When encountering a closing paranthesis pop the stack and see if they match

```csharp
public bool IsValid(string s) {
	var k = new Stack<char>();

	foreach(char c in s) {

		if (c == '(') { k.Push(')'); continue; }

		if (c == '{') { k.Push('}'); continue; }

		if (c == '[') { k.Push(']'); continue; }

		if (k.Count == 0 || c != k.Pop()) return false;
	}

	return k.Count == 0;
}
```

# 21. Merge Two Sorted Lists
## Difficulty: Easy
## Topics: Linked List, Recursion

### Solution 1: Iterative
```csharp
public class Solution
{
    public ListNode MergeTwoLists(ListNode list1, ListNode list2)
    {
        ListNode dummy = new ListNode(0);
        ListNode result = dummy;

        // While both lists are still not null / one list still has values
        while (list1 != null && list2 != null)
        {
            // Place the lower value between the 2 list nodes at the current result node and move to the next element of the list

            if (list1.val <= list2.val)
            {
                result.next = list1;
                list1 = list1.next;
            }
            else
            {
                result.next = list2;
                list2 = list2.next;
            }

            result = result.next;
        }

        // If list1 is null then add the remainder of list to the result list
        result.next = list1 ?? list2;

        return dummy.next;
    }
}
```

# 22. Generate Parantheses
## Difficulty: Medium
## Topics: Backtracking, Dynamic Programming

### Soltution 1 - Backtracking

```
https://leetcode.com/problems/generate-parentheses/solutions/1131364/clear-and-simple-explanation-with-intuition-100-faster
Concept: In every backtracking problem , there are two things to keep in mind , which we will explore here as well :

Base Case: Every problem of backtracking has some base case which tells us at which point we have to stop with the recursion process. In our case, when the length of our string has reached the maximum length(n*2), we stop with the recursion for that case and that is our base case.

Conditions: On observing carefully we find that there are two conditions present:

For adding (: If number of opening brackets(open) is less than the the given length(n) i.e.
if max<n, then we can add (,else not.
For adding ): If number of close brackets(close) is less than the opening brackets(open), i.e.
if open<close, we can add ), else not
```

```csharp
public class Solution
{
    public IList<string> GenerateParenthesis(int n)
    {
        var result = new List<string>();
        Solve(result, "", 0, 0, n);
        return result;
    }

    private void Solve(IList<string> result, string current, int open, int close, int max)
    {
        // Base case: if the current string length is equal to 2 * max, there are max number of pairs in the string - the string is a result
        if (current.Length == max * 2)
        {
            result.Add(current);
            return;
        }

        // If the number of open parentheses is lower than the max number of pairs -> try adding a new opening parenthesis
        if (open < max)
        {
            Solve(result, current + "(", open + 1, close, max);
        }

        // If the number of open parentheses is bigger than the number of closed parentheses -> try adding a closing parenthesis to make a pair
        // This condition makes it so the opening parentheses can never be more than closing ones, '(()' cannot add a new '('
        if (open > close)
        {
            Solve(result, current + ")", open, close + 1, max);
        }
    }
}
```

# 26. Remove Duplicates from Sorted Array
## Difficulty: Medium
## Topics: Two Pointers

### Solution 1 - Two pointers
- Left pointer is on the last element without a duplicate, right pointer is incremented until the end of the array
- In each iteration check if `nums[left]` != `nums[right]`
  - If not then increment right pointer (move right until a new element is found)
  - If yes then overwrite the element at `left+1` with the element at `nums[right]` and increment left pointer (overwrite duplicate elements with new ones)
- Return `left` as the number of unique elements

```csharp
public int RemoveDuplicates(int[] nums)
{
	int left = 0;
	int right = 1;
	
	while (right < nums.Length)
	{
		// if a new element is found - overwrite duplicate at left + 1 with the new element
		// move left to point to the new unique element
		if (nums[right] != nums[left])
		{
			nums[left + 1] = nums[right];
			left++;
		}

		right++;
	}

	return left + 1;
}
```

# 27. Remove Element
## Difficulty: Easy
## Topics: Two Pointers

### Solution 1 - Two Pointers
- Similar to #26 - use two pointers
  - left is the index where val was found, the index where a new value needs to be put
  - right is the index where the first non val value is found
- In each iteration check if `nums[right] != val`
  - if not then keep incrementing right
  - if yes then overwrite `nums[left]` with `nums[right]`

```csharp
public int RemoveElement(int[] nums, int val)
{
	int left = 0;
	int right = 0;

	while (right < nums.Length)
	{
		if (nums[right] != val)
		{
			nums[left] = nums[right];
			left++;
		}

		right++;
	}

	return left;
}
```

### Solution 1.1 - 
```csharp
public int RemoveElement(int[] nums, int val)
{
	int left = 0;

	for (int right = 0; right < nums.Length; right++)
	{
		if (nums[right] != val)
		{
			nums[left] = nums[right];
			left++;
		}
	}

	return left;
}
```

# 33. Search in Rotated Sorted Array
## Difficulty: Medium
## Topics: Binary Search

### Solution 
- The array is split into 2 parts, both are sorted but in different directions -> look at comments

```csharp
public class Solution
{
    public int Search(int[] nums, int target)
    {
        int l = 0, r = nums.Length - 1;

        while (l <= r)
        {
            int mid = (l + r) / 2;
            if (target == nums[mid])
            {
                return mid;
            }

            // If left side is sorted
            if (nums[l] <= nums[mid])
            {
                // If left side is sorted and target is > middle
                // -> need to search in the right subarray because mid is the biggest element in the left subarray
                // If left side is sorted and target is less than the minimum element
                // -> need to search in the right subarray because nums[l] is the smallest element in the left subarray
                if (target > nums[mid] || target < nums[l])
                {
                    l = mid + 1;
                }
                // Otherwise search in the left subarray
                else
                {
                    r = mid - 1;
                }
            }
            // If right side is roted
            else
            {
                // If right side is sorted and target is less than mid
                // -> need to search in left subarray because mid will be the lowest value in the right subarray
                // If right side is sorted and target is bigger than nums[right]
                // -> need to search in the left subarray because nums[r] is the biggest element in the right subarray
                if (target < nums[mid] || target > nums[r])
                {
                    r = mid - 1;
                }
                else
                {
                    l = mid + 1;
                }
            }
        }

        return -1;
    }
}
```

# 35. Serach Insert Rotation
## Difficulty: Easy
## Topics: Binary Search

### Solution
- Return left pointer instead of -1 if not found 

```csharp
public class Solution
{
    public int SearchInsert(int[] nums, int target)
    {
        int left = 0;
        int right = nums.Length - 1;

        int lastIndex = 0;
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
        return left;
    }
}
```

# 36. Valid Sudoku
## Difficulty: Medium
## Topics: Hash table

### Solution 1 - Hashset
- Can't have same number in the row, column, or 3x3 subgrid
- To check for duplicates use a hashset using a string made up of the number and the position
- To check for the duplicates inside a box divide row and column by 3 => rows 0,1,2 - box 0, rows 3,4,5 - box 1

```csharp
public bool IsValidSudoku(char[][] board)
{
	HashSet<string> seen = new();
	for (int row = 0; row < 9; row++)
	{
		for (int col = 0; col < 9; col++)
		{
			char current = board[row][col];
			if (current == '.') continue;

			if (!seen.Add(current + "in row" + row) ||
				!seen.Add(current + "in column" + col) ||
				!seen.Add(current + "in block" + row / 3 + "-" + col / 3))
				return false;
		}
	}

	return true;
}
```

# 49. Group Anagrams
## Difficulty: Medium
## Topics: Hash Map
### Solution 1 - Dictionary with sorted word as the key
- Use a dictionary to keep the words that make up the same anagram
- Use the sorted word as the key for the dictionary

```csharp
public IList<IList<string>> GroupAnagrams(string[] strs)
{
	var anagrams = new Dictionary<string, IList<string>>();

	foreach (var str in strs)
	{
		// the key is a new string that is the sorted original word
		char[] ch = str.ToCharArray();
		Array.Sort(ch);
		string key = new string(ch);

		if (!anagrams.ContainsKey(key))
		{
			anagrams[key] = new List<string>();
		}

		anagrams[key].Add(str);
	}

	return anagrams.Values.ToList();
}
```

### Solution 2 - Dictionary with sum of chars as the key
- Use a dictionary to store the anagrams, the key is the sum of the chars and the value is a list of all the anagrams that have that sum / those letters
- Because the sum can be made with different words, the key is a string made up of the count of each char

```csharp
public IList<IList<string>> GroupAnagrams(string[] strs)
{
	var anagrams = new Dictionary<string, IList<string>>();

	foreach (var str in strs)
	{
		int[] charsCount = new int[26];
		
		foreach (char c in str)
			charsCount[c - 'a']++;
		
		string key = string.Join(", ", charsCount);

		if (!anagrams.ContainsKey(key))
		{
			anagrams[key] = new List<string>();
		}
		anagrams[key].Add(str);
	}

	return anagrams.Values.ToList();
}
```

### Solutin 3 - LINQ GroupBy
```csharp
public IList<IList<string>> GroupAnagrams(string[] strs)
{
	return strs
		.GroupBy(str => new string(str.OrderBy(c => c).ToArray()))
		.Select(g => g.ToList() as IList<string>)
		.ToList();
}
```

# 58. Length of Last Word
## Difficulty: Easy
## Topics: String

### Solution 1 
```csharp
public class Solution {
    public int LengthOfLastWord(string s)
    {
        s = s.Trim();
        int length = 0;
        
        for (int i = s.Length - 1; i >= 0; i--)
        {
            if (s[i] != ' ')
            {
                length++;
            }
            else
            {
                return length;
            }
        }

        return length;
    }
}
```

# 74. Search a 2D Matrix
## Difficulty: Medium
## Topics: Binary Search

### Solution 1 -
```csharp
public class Solution
{
    public bool SearchMatrix(int[][] matrix, int target)
    {
        int rowLength = matrix.Length;
        int colLength = matrix[0].Length;
        int left = 0;
        int right = rowLength * colLength - 1;
        
        int minValue = matrix[0][0];
        int maxValue = matrix[rowLength - 1][colLength - 1];
        if (target < minValue || target > maxValue) return false;

        while (left <= right)
        {
            int mid = left + (right - left) / 2;
            
            int row = mid / n; 
            int col = mid % n;
            
            int midVal = matrix[row][col];

            if (midVal == target) return true;

            if (midVal < target)
                left = mid + 1;
            else
                right = mid - 1;
        }

        return false;
    }
}
```

### Solution 2 -
- Each row contains an array of sorted elements, so each element in a row is in interval `leftElement < element < rightElement`
- Searching is done

```csharp
public class Solution
{
    public bool SearchMatrix(int[][] matrix, int target)
    {
        int row = 0;
        int col = 0;
        int rowLength = matrix.Length;
        int colLength = matrix[0].Length;

        int minValue = matrix[0][0];
        int maxValue = matrix[rowLength - 1][colLength - 1];

		// if target is less than min value of the matrix or bigger than max value -> false
        if (target < minValue || target > maxValue) return false;

        while (row < rowLength && col < colLength)
        {
            // First and last elements of the current sorted array 
            int left = matrix[row][col];
            int right = matrix[row][colLength - 1];

            // If target is bigger than the last element of current row it means that we need to try searching in the next row with bigger elements 
            // jump to next row and reset column index
            if (target > right)
            {
                row++;
                col = 0;
                continue;
            }

            // If target can be found in the current row / sorted array then search for it
            if (target >= left && target <= right)
            {
                while (col < colLength)
                {
                    if (matrix[row][col] == target)
                    {
                        return true;
                    }
                    
                    // Jump to the next column
                    col++;
                }
            }
            // If target is bigger than the max of the last row, but cannot be found in current row -> false
            else
            {
                return false;
            }
        }

        return false;
    }
}
```

# 83. Remove Duplicates from Sorted List
## Difficulty: Easy
## Topics: Linked List

### Solution 
- Compare current node value with next node value, if they match then skip next node

```csharp
public class Solution
{
    public ListNode DeleteDuplicates(ListNode head)
    {
        if (head == null)
            return null;

        if (head.next == null)
            return head;

        ListNode dummy = head;

        // Keep checking until current node has a node after it - if the current node doesn't have any other nodes after it then the list can't have duplicates
        while (head.next != null)
        {
            // If next node is a duplicate - jump over the next node
            if (head.val == head.next.val)
            {
                head.next = head.next.next;
            }
            // Otherwise continue the list as normal
            else
            {
                head = head.next;
            }
        }

        return dummy;
    }
}
```

# 88. Merge Sorted Array
## Difficulty: Easy
## Topics: Two Pointers, Sorting

### Solution 1 - 
- Use 2 pointers, one for each array starting on the right side
- Only need to process until index2 is 0, nums1 is already sorted so if all the elements of num2 are placed correctly then its already sorted
- Start comparing elements from the end
  - if `nums1[index1]` > `nums2[index2]` then `nums1[index1]` needs to be set on the value

```csharp
public void Merge(int[] nums1, int m, int[] nums2, int n)
{
	int index1 = m - 1;
	int index2 = n - 1;
	int indexResult = m + n - 1;

	// only need to process the elements of nums2, nums1 is already sorted
	while (index2 >= 0)
	{
		if (index1 >= 0 && nums1[index1] > nums2[index2])
		{
			nums1[indexResult] = nums1[index1];
			indexResult--;
			index1--;
		}
		else
		{
			nums1[indexResult] = nums2[index2];
			indexResult--;
			index2--;
		}
	}
}
```

# 121. Best Time to Buy and Sell Stock
## Difficulty: Easy
## Topics: Dynamic Programming, Two pointers
### Method 1 - Two Pointers
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
		// if the trade is profitable check if the profit is bigger than the current profit
		if (prices[left] < prices[right])
		{
			int profit = prices[right] - prices[left];
			if (profit > maxProfit)
			{
				maxProfit = profit;
			}
		}
		// if the trade isn't profitable (right value is smaller than left value), then update left value to the current smallest value and continue searching
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
## Topics: Two Pointers
### Method 1 - Two Pointers
- Use to pointers to store the current chars
- Left pointer starts at index 0 and Right pointers starts at the last char
- In each iteration move left pointer to the right and right pointer to the left, then check if the chars are the same

```csharp
public bool IsPalindrome(string s)
{
	if (String.IsNullOrEmpty(s)) return true;

	int leftIndex = 0;
	int rightIndex = s.Length - 1;

	while (leftIndex < rightIndex)
	{
		// increment left index until a letter is found
		while (leftIndex < rightIndex && !Char.IsLetterOrDigit(s[leftIndex])) leftIndex++;

		// increment right index until a letter is found
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
<br/>

### 
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
<br/>

###
```csharp
public bool IsPalindrome(string s)
{
	string cleaned = new string(s.ToLower().Where(char.IsLetterOrDigit).ToArray());
	return cleaned.SequenceEqual(cleaned.Reverse());
}
```

# 128. Longest Consecutive Sequence
## Difficulty: Medium
## Topics: Hash Map, Union find

### Solution 1 - Sorting
#### Beats: 33%
```csharp
public int LongestConsecutive(int[] nums)
{
	if (nums.Length == 0) return 0;

	Array.Sort(nums);
	int maxLength = 1;
	int currentLength = 1;

	for (int i = 1; i < nums.Length; i++)
	{
		// Skip duplicates
		if (nums[i] == nums[i - 1])
			continue;

		// If current element is consecutive to previous
		if (nums[i] == nums[i - 1] + 1)
		{
			currentLength++;
		}
		else
		{
			// Reset current streak
			currentLength = 1;
		}

		maxLength = Math.Max(maxLength, currentLength);
	}

	return maxLength;
}
```

### Solution 2 - Hashset
#### Beats: 74%
- Put all numbers in a hashset to remove duplicates and enable O(1) lookups
- Only start counting sequences from numbers that have no predecessors (num-1 not in set)
- For each valid starting number, count consecutive elements by checking `num+1`, `num+2`, ... .

```csharp
public int LongestConsecutive(int[] nums)
{
	if (nums.Length == 0)
	{
		return 0;
	}

	HashSet<int> hashSet = new HashSet<int>(nums);

	int maxLength = 1;
	foreach (int num in hashSet)
	{ 
		// Check if current number is the start of a new sequence
		if (!hashSet.Contains(num - 1))
		{
			int length = 1;
			while (hashSet.Contains(num + length))
			{
				length++;
			}

			maxLength = Math.Max(length, maxLength);
		}
	}

	return maxLength;
}
```

# 136. Single Number
## Difficulty: Easy
## Topics: Bit manipulation

### Solution 1 - Using XOR
- XOR'ing a number with itself will make the result 0 -> the number inside the array without a duplicate will not be removed

```csharp
public class Solution
{
    public int SingleNumber(int[] nums)
    {
        int result = 0;
        foreach (var num in nums)
        {
            result ^= num;
        }

        return result;
    }
}
```

# 141. Linked List Cycle
## Difficulty: Easy
## Topics: Linked List, Two Pointers (Fast and Slow)

### Solution - Fast and Slow pointers
- Use 2 pointers one that moves one node at a time and one that moves 2 nodes at a time
- If the list has a cycle they will meet somewhere

```csharp
public class Solution
{
    public bool HasCycle(ListNode head)
    {
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null)
        {
            slow = slow.next;
            fast = fast.next.next;

            if (slow == fast)
                return true;
        }

        return false;
    }
}
```

# 150. Evaluate Reverse Polish Notation
## Difficulty: Medium
## Topics: Stack

### Solution 1 
- Process each token,
  - if it's a number then push it to the stack
  - if it's an operator then pop 2 numbers from the stack, do that operation, and push the result to the stack

```csharp
public int EvalRPN(string[] tokens)
{
	Stack<int> numbers = new();
	int a, b;
	
	foreach (var token in tokens)
	{
		switch (token)
		{
			case "+":
				b = numbers.Pop();
				a = numbers.Pop();
				numbers.Push(a + b);
				break;
			case "-":
				b = numbers.Pop();
				a = numbers.Pop();
				numbers.Push(a - b);
				break;
			case "*":
				b = numbers.Pop();
				a = numbers.Pop();
				numbers.Push(a * b);
				break;
			case "/":
				b = numbers.Pop();
				a = numbers.Pop();
				numbers.Push(a / b);
				break;
			default:
				if (int.TryParse(token, out int number))
				{
					numbers.Push(number);
				}
				break;
		}
	}

	return numbers.Pop();
}
```

### Solution 2 - Dictionary with funcs for operators
```csharp
public int EvalRPN(string[] tokens)
{
	Stack<int> numbers = new();
	var operators = new Dictionary<string, Func<int, int, int>>
	{
		{ "+", (a, b) => a + b },
		{ "-", (a, b) => a - b },
		{ "*", (a, b) => a * b },
		{ "/", (a, b) => a / b }
	};

	foreach (var token in tokens)
	{
		if (operators.TryGetValue(token, out var operation))
		{
			int b = numbers.Pop();
			int a = numbers.Pop();
			numbers.Push(operation(a, b));
		}
		else if (int.TryParse(token, out int number))
		{
			numbers.Push(number);
		}
	}
	
	return numbers.Pop();
}
```

# 151. Reverse Words in a String
## Difficulty: Medium XD
## Topics: String

### Solution 1 - 
- Trim and split the array
- Start looking at words from the end and append them using a stringBuilder

```csharp
using System.Text;

public class Solution
{
    public string ReverseWords(string s)
    {
        s = s.Trim();
        var words = s.Split(' ', StringSplitOptions.RemoveEmptyEntries);
        var stringBuilder = new StringBuilder();

        for (int i = words.Length - 1; i >= 0; i--)
        {
            stringBuilder.Append(words[i]);
            if (i > 0)
            {
                stringBuilder.Append(' ');
            }
        }

        return stringBuilder.ToString();
    }
}
```

### Solution 2 - Sort and reverse
```csharp
public class Solution {
    public string ReverseWords(string s) {
        s = s.Trim();

        string[] words = s.Split(new char[] { ' ' }, StringSplitOptions.RemoveEmptyEntries);

        Array.Reverse(words);

        return string.Join(" ", words);
    }
}
```

# 153. Find Minimum in Rotated Sorted Array
## Difficulty: Medium
## Topics: Binary Search

### Solution 
- If mid value is bigger than right most element - it means the rotated part in on the right with the smallest elements

```csharp
public class Solution
{
    public int FindMin(int[] nums)
    {
        int left = 0;
        int right = nums.Length - 1;

        while (left <= right)
        {
            int mid = (left + right) / 2;
            int midValue = nums[mid];

            // If mid value is bigger than right most element - it means the rotated part in on the right with the smallest elements
            if (midValue > nums[nums.Length - 1])
            {
                left = mid + 1;
            }
            else
            {
                right = mid - 1;
            }
        }

        return nums[left];
    }
}
```

# 155. Min Stack
## Difficulty: Medium
## Topics: Stack, Design

### Solution 1 - 2 Stacks
- Use 2 stacks, one that stores values normally and one that always store the min value at every moment
- When pushing - push value normally to main stack, for minStack push the min between `value` and `currentMinimum` 
  - Minstack will contain the min value for every value inserted in the stack

```csharp
public class MinStack
{
	Stack<int> stack;
	Stack<int> minStack;
	int minValue;

	public MinStack()
	{
		stack = new Stack<int>();
		minStack = new Stack<int>();
	}

	public void Push(int val)
	{
		if(stack.Count == 0)
		{
			minValue = val;
		}
		
		stack.Push(val);
		
		minValue = Math.Min(minValue, val);
		minStack.Push(minValue);
	}

	public void Pop()
	{
		stack.Pop();
		minStack.Pop();
		if (minStack.Count > 0)
		{
			minValue = minStack.Peek();
		}
	}

	public int Top()
	{
		return stack.Peek();
	}

	public int GetMin()
	{
		return minValue;
	}
}
```

### Solution 2 - Tuple with value and min
```csharp
public class MinStack
{
	private Stack<(int Value, int Min)> _stack;

	public MinStack()
	{
		_stack = new Stack<(int Value, int Min)>();
	}

	public void Push(int val)
	{
		// if stack count is 0 push the first element without extra logic
		if (_stack.Count == 0)
		{
			_stack.Push((val, val));
			return;
		}

		// when pushing new element check the current min value
		var prevMin = _stack.Peek().Min;
		_stack.Push((val, val < prevMin ? val : prevMin));
	}

	public void Pop() => _stack.Pop();
	public int Top() => _stack.Peek().Value;
	public int GetMin() => _stack.Peek().Min;
}
```

# 160. Intersection of Two Linked Lists
## Difficulty: Easy
## Topics: Linked List

### Solution - Find lists lengths and adjust the starting point of the bigger list
```csharp
public class Solution
{
    public ListNode GetIntersectionNode(ListNode headA, ListNode headB)
    {
        // Find length of listA and listB
        int lenA = ListLength(headA);
        int lenB = ListLength(headB);

        // Align the lists starting positions, move the starting point of the bigger list until the lists have the same length
        // Will make it so the list with more elements will start in the same place as the smaller list
        while (lenA > lenB)
        {
            headA = headA.next;
            lenA--;
        }

        while (lenB > lenA)
        {
            headB = headB.next;
            lenB--;
        }
        
        while (headA != headB)
        {
            headA = headA.next;
            headB = headB.next;
        }

        return headA;
    }

    private int ListLength(ListNode head)
    {
        int length = 0;
        while (head != null)
        {
            head = head.next;
            length++;
        }

        return length;
    }
}
```

# 167. Two Sum II - Input Array is Sorted
## Difficulty: Medium
## Topics: Two Pointers, Binary Search

### Solution 1 - Two pointers
- There is always one solution for each array
- Because the array is sorted left side contains lower numbers and right side higher numbers
- Use 2 pointers left and right and compare their sum to the target:
  - If sum > target then the sum need to be lower, decrement right
  - If sum < target then the sum need to be higher, increment left

```csharp
public int[] TwoSum(int[] numbers, int target)
{
	int left = 0;
	int right = numbers.Length - 1;

	while (left < right)
	{
		int sum = numbers[left] + numbers[right];
		if (sum == target) break;
		if (sum < target) left++;
		if (sum > target) right--;
	}

	return new int[] { left + 1, right + 1 };
}
```

### Solution 2 - (Worse) Use dictionary for complement like Two Sum I
```csharp
public int[] TwoSum(int[] numbers, int target)
{
	Dictionary<int, int> dict = new Dictionary<int, int>();

	for (int i = 0; i < numbers.Length; i++)
	{
		var complement = target - numbers[i];
		if (dict.ContainsKey(complement))
		{
			return [dict[complement] + 1, i + 1];
		}

		dict[numbers[i]] = i;
	}

	return numbers;
}
```

# 169. Majority Element
## Difficulty: Easy
## Topics: Hash table, Heap, Sorting, Counting, Divide and conquer

### Solution 1 - Moore's Voting Algorithm
- Initialize two variables: `count` and `candidate`. Set `count` to 0 and `candidate` to an arbitrary value
- Iterate through the array nums:
  - a. If `count` is 0, assign the current element as the new `candidate` and increment `count` by 1
  - b. If the `current` element is the same as the `candidate`, increment `count` by 1
  - c. If the `current` element is different from the `candidate`, decrement `count` by 1
- After the iteration, the `candidate` variable will hold the majority element

```csharp
public int MajorityElement(int[] nums)
{
	int count = 0;
	int candidate = 0;

	foreach (int num in nums)
	{
		if (count == 0)
		{
			candidate = num;
		}

		if (num == candidate)
		{
			count++;
		}
		else
		{
			count--;
		}
	}

	return candidate;
}
```

### Solution 2 - Frequency Map
```csharp
public int MajorityElement(int[] nums)
{
	int majoritySize = nums.Length / 2;

	var frequencyMap = new Dictionary<int, int>();
	foreach (var num in nums)
	{
		if (!frequencyMap.ContainsKey(num))
		{
			frequencyMap[num] = 0;
		}

		frequencyMap[num]++;

		if (frequencyMap[num] > majoritySize)
		{
			return num;
		}
	}

	// there's always a majority element
	return -1;
}
```


### Solution 3 - Sort array and get element at n/2
```csharp
public int MajorityElement(int[] nums)
{
	Array.Sort(nums);
	int majority = nums.Length / 2;
	return nums[majority];
}
```

# 187. Repeated DNA Sequences
## Difficulty: Medium
## Topics: Hash Table, Bit manipulation, Sliding Window, Rolling Hash

### Solution 1 - Hashsets
- Use a substring to find the sequence of nucleotides
- Use 2 hashsets, one that contains the seen elements and one that contains the elements that appear more than once
- (Could also use just a dictionary and return all the keys that appear more than once)

```csharp
public class Solution
{
    public IList<string> FindRepeatedDnaSequences(string s)
    {
        var seen = new HashSet<string>();
        var repeated = new HashSet<string>();

        for (int i = 0; i < s.Length - 9; i++)
        {
            var key = s.Substring(i, 10);
            if (!seen.Add(key))
                repeated.Add(key);
        }

        var result = new List<string>();
        foreach (var elem in repeated)
        {
            result.Add(elem);
        }

        return result;
    }
}
```


# 203. Remove Linked List Elements
## Difficulty: Easy
## Topics: Linked List, Recursion

### Solution 
- Store the previous value, when a node with the target value is found set the previous node next pointer to the next node

```csharp
public class Solution
{
    public ListNode RemoveElements(ListNode head, int val)
    {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        
        ListNode prev = dummy;
        ListNode curr = head;
        
        while(curr != null)
        {
            // Make previous skip the current node
            if(curr.val == val)
                prev.next = curr.next;
            // If current value is the same with target don't update previous - there are situations where the first node has the target value and this will case it to break
            else
                prev = curr;
            curr = curr.next;
        }
        
        return dummy.next;
    }
}
```

# 205. Isomorphic String
## Difficulty: Easy
## Topics: Hash Table

### Solution 1 - 2 dictionaries
```csharp
public class Solution {
	public bool IsIsomorphic(string s, string t)
	{
		var sToT = new Dictionary<char, char>();
		var tToS = new Dictionary<char, char>();

		for (int i = 0; i < s.Length; i++)
		{
			char sChar = s[i];
			char tChar = t[i];

			// check if s was mapped to a t
			if (sToT.ContainsKey(sChar))
			{
				if (sToT[sChar] != tChar) return false;
			}
			// check if t was mapped to an s
			else if (tToS.ContainsKey(tChar))
			{
				if (tToS[tChar] != sChar) return false;
			}
			else
			{
				sToT[sChar] = tChar;
				tToS[tChar] = sChar;
			}
		}

		return true;
	}
}
```

### Solution 1' - 1 dictionary
- Use a dictionary to store the mappings from the letters of s to the letters of t
- Loop over all chars of the words and check if there's a mapping from `sChar` to `tChar`
  - if a mapping exists from `sChar` to `tChar` check if `tChar` is equal to the existing mapping
  - if there is no mapping from `sChar` to `tChar` check if `tChar` was already used in another mapping

```csharp
public bool IsIsomorphic(string s, string t)
{
	var sToT = new Dictionary<char, char>();

	for (int i = 0; i < t.Length; i++)
	{
		char sChar = s[i];
		char tChar = t[i];

		// if dict doesn't contain a mapping for the original char
		if (!sToT.TryGetValue(sChar, out var existingMapping))
		{
			// check if second word char is used in multiple places
			// if the mapping was already used for another char return false
			// ex: "badc" -> "baba", dict contains b->b, a->a, and now d->b which is wrong
			if (sToT.ContainsValue(tChar)) return false;

			// put the mapping in the dict
			sToT[sChar] = tChar;
			continue;
		}

		// check if first word char is used in multiple places
		// if the mapping that needs to be done for the char of s is different from the existing mapping -> return false
		// ex: "abb" -> "abc", b is used for both b->b and b->c
		if (tChar != existingMapping) return false;
	}

	return true;
}
```

### Solution 2 - Char array for frequencies
```csharp
public bool IsIsomorphic(string s, string t)
{
	int[] map1 = new int[128]; // Stores frequency of s
	int[] map2 = new int[128]; // Stores frequency of t

	for (int i = 0; i < s.Length; i++)
	{
		char s_ch = s[i];
		char t_ch = t[i];

		// if there is no mapping from s->t or t->s
		if (map1[s_ch] == 0 && map2[t_ch] == 0)
		{
			map1[s_ch] = t_ch;
			map2[t_ch] = s_ch;
		}
		else if (map1[s_ch] != t_ch || map2[t_ch] != s_ch)
		{
			return false;
		}
	}

	return true;
}
```

# 209. Minimum Size Subarray Sum
## Difficulty: Medium
## Topics: Sliding Window (Dynamic)

### Solution
- Use a sum to store the sum of all the elments in the window
- While the condition is true !!! use a while to shrink the window to try and find the smallest possible result

```csharp
public class Solution
{
    public int MinSubArrayLen(int target, int[] nums)
    {
        int result = Int32.MaxValue;

        int sum = 0;

        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            sum += nums[right];

            while (sum >= target)
            {
                result = Math.Min(result, right - left + 1);
                sum -= nums[left];
                left++;
            }
        }

        return result == Int32.MaxValue ? 0 : result;
    }
}
```

# 215. Kth Largest Element in an Array
## Difficulty: Medium
## Topics: Divide and Conquer, Heap, Quickselect
### Solution - Heap
- Use a minheap of size k to process items
- If heap size size is > k then dequeue
- Return the root of the heap as the k-th element

```csharp
public int FindKthLargest(int[] nums, int k)
{
	PriorityQueue<int, int> minHeap = new PriorityQueue<int, int>();
	foreach (var num in nums)
	{
		minHeap.Enqueue(num, num);

		if (minHeap.Count > k)
		{
			minHeap.Dequeue();
		}
	}

	return minHeap.Dequeue();
}
```

### Solution - Sort the array and the the k-th element from the end
- Is faster than heap method, maybe because k ~= n in the tests?
```csharp
public int FindKthLargest(int[] nums, int k)
{
	Array.Sort(nums);
	return nums[nums.Length - k];
}
```

# 217. Contains Duplicate
## Difficulty: Easy
## Topics: Hash table, Sorting

### Solution 1 - Check if hashset contains
```csharp
public bool ContainsDuplicate(int[] nums) {
	HashSet<int> set = new HashSet<int>();
	foreach (var num in nums)
	{
		if (set.Contains(num))
		{
			return true;
		}
		set.Add(num);
	}

	return false;
}
```

### Solution - Sort array and check if next element is duplicate
```csharp
public bool ContainsDuplicate(int[] nums)
{
	Array.Sort(nums);
	for (int i = 0; i < nums.Length - 1; i++)
	{
		if (nums[i] == nums[i + 1])
		{
			return true;
		}
	}

	return false;
}
```

# 225. Implment Stack using Queues
## Difficulty: Easy
## Topics: Stack, Queue, Design

### Solution 2 - One Queue
```csharp
public class MyStack
{
    // Use a single queue to simulate the stack
    private Queue<int> queue;

    public MyStack()
    {
        queue = new Queue<int>();
    }

    // Push element x onto the stack
    public void Push(int x)
    {
        // Add the new element to the queue
        queue.Enqueue(x);

        // Rotate the queue so that the new element is at the front
        // This simulates the LIFO (Last-In-First-Out) behavior of a stack
        int size = queue.Count;
        for (int i = 0; i < size - 1; i++)
        {
            // Move the front element to the back of the queue
            queue.Enqueue(queue.Dequeue());
        }
    }

    // Remove and return the element on the top of the stack
    public int Pop()
    {
        // Since the top element is always at the front of the queue, simply dequeue it
        return queue.Dequeue();
    }

    // Return the element on the top of the stack without removing it
    public int Top()
    {
        // Since the top element is always at the front of the queue, simply peek at it
        return queue.Peek();
    }

    // Check if the stack is empty
    public bool Empty()
    {
        // The stack is empty if the queue is empty
        return queue.Count == 0;
    }
}
```

### Solution 1 - Two Queues (Similar to #232)

```csharp
public class MyStack
{
    // queue1 is the main queue used to store stack elements
    private Queue<int> queue1;
    
    // queue2 is a temporary queue used during the Push operation to reorder elements
    private Queue<int> queue2;

    public MyStack()
    {
        // Initialize both queues
        queue1 = new Queue<int>();
        queue2 = new Queue<int>();
    }

    // Push element x onto the stack
    public void Push(int x)
    {
        // Move all elements from queue1 to queue2
        // This ensures the new element x is added to the front of queue1
        int q1Size = queue1.Count;
        for (int i = 0; i < q1Size; i++)
        {
            queue2.Enqueue(queue1.Dequeue());
        }

        // Add the new element x to queue1
        queue1.Enqueue(x);

        // Move all elements back from queue2 to queue1
        // This restores the order of elements, with x at the front of queue1
        while (queue2.Count > 0)
        {
            queue1.Enqueue(queue2.Dequeue());
        }
    }

    // Remove and return the element on the top of the stack
    public int Pop()
    {
        // Since queue1 maintains the stack order, simply dequeue from queue1
        return queue1.Dequeue();
    }

    // Return the element on the top of the stack without removing it
    public int Top()
    {
        // Since queue1 maintains the stack order, simply peek at the front of queue1
        return queue1.Peek();
    }

    // Check if the stack is empty
    public bool Empty()
    {
        // The stack is empty if queue1 is empty
        return queue1.Count == 0;
    }
}
```

# 232. Implementing Queue with Stacks
## Difficulty: Easy
## Topics: Stack, Queue, Design

### Solution - Use 2 stacks
- One stack is used just for pushing elements, one is used to pop and peek the elements
- To Make the order of the elements the same as a queue, when an element is popped move them all from stack1 to stack2, now oldest elements from stack1 will pe placed at the bottom of stack2 and removed first

```csharp
namespace LeetCode;

public class MyQueue
{
    // stack1 is used for pushing elements into the queue
    private Stack<int> stack1;
    
    // stack2 is used for popping and peeking elements from the queue
    private Stack<int> stack2;

    public MyQueue()
    {
        // Initialize both stacks
        stack1 = new Stack<int>();
        stack2 = new Stack<int>();
    }

    // Push element x to the back of the queue
    public void Push(int x)
    {
        // Always push the new element onto stack1
        stack1.Push(x);
    }

    // Remove and return the element from the front of the queue
    public int Pop()
    {
        // If stack2 is empty, transfer all elements from stack1 to stack2
        // This reverses the order of elements, making the oldest element in stack1 the top of stack2
        if (stack2.Count == 0)
        {
            while (stack1.Count > 0)
            {
                stack2.Push(stack1.Pop());
            }
        }
        // Pop and return the top element from stack2
        return stack2.Pop();
    }

    // Return the element at the front of the queue without removing it
    public int Peek()
    {
        // If stack2 is empty, transfer all elements from stack1 to stack2
        if (stack2.Count == 0)
        {
            while (stack1.Count > 0)
            {
                stack2.Push(stack1.Pop());
            }
        }
        // Return the top element from stack2 without popping it
        return stack2.Peek();
    }

    // Check if the queue is empty
    public bool Empty()
    {
        // The queue is empty if both stacks are empty
        return stack1.Count == 0 && stack2.Count == 0;
    }
}
```

# 234. Palindrome Linked List
## Difficulty: Easy
## Topics: Linked List, Two Pointers (Slow and Fast)

### Solution 1 - Slow and fast pointers to find middle, reverse the list
```csharp
public class Solution
{
    public bool IsPalindrome(ListNode head)
    {
        if (head == null || head.next != null)
            return true;

        ListNode slow = head;
        ListNode fast = head;

        // Use slow and fast pointers to find the middle
        // When fast pointer reaches the end, slow will be in the middle of the list
        while (fast != null && fast.next != null)
        {
            slow = slow.next;
            fast = fast.next.next;
        }


        // Reverse the second half of the list
        ListNode prev = null;
        ListNode current = slow;
        while (current != null)
        {
            ListNode nextNode = current.next;
            current.next = prev;
            prev = current;
            current = nextNode;
        }

        // Compare each node in the halves of the list
        ListNode firstHalf = head;
        ListNode secondHalf = prev;
        while (secondHalf != null)
        {
            if (firstHalf.val != secondHalf.val)
                return false;

            firstHalf = firstHalf.next;
            secondHalf = secondHalf.next;
        }

        return true;
    }
}
```

# 238. Product of array except self
## Difficulty: Medium
## Topics: Prefix sum

### Solution 1 - Brute force
#### Beats: Time Limit Exceeded :\
- 2 for loops, outer loop over all the elements, inner loop that calculates the product for all the elements except the current one 
```csharp
public int[] ProductExceptSelf(int[] nums)
{
	var result = new int[nums.Length];
	for (int i = 0; i < nums.Length; i++)
	{
		int temp = 1;
		for (int j = 0; j < nums.Length; j++)
		{
			if (i != j)
			{
				temp *= nums[j];
			}
		}

		result[i] = temp;
	}

	return result;
}
```

### Solution 2 - Suffix and prefix
#### Beats: 30.57%
- Use 2 arrays for each element
  - prefix array contains the product of all the elements before `i`
  - postfix array contains the product of all the elements after `i`
  - product of the array except `element[i]` is `prefix[i] * postfix[i]`

```csharp
public int[] ProductExceptSelf(int[] nums)
{
    int n = nums.Length;
    var result = new int[n];
    var prefix = new int[n];
    var postfix = new int[n];

    // Compute prefix products
    prefix[0] = 1; // No elements to the left of first element
    for (int i = 1; i < n; i++)
    {
        prefix[i] = prefix[i - 1] * nums[i - 1];
    }

    // Compute postfix products
    postfix[n - 1] = 1; // No elements to the right of last element
    for (int i = n - 2; i >= 0; i--)
    {
        postfix[i] = postfix[i + 1] * nums[i + 1];
    }

    for (int i = 0; i < n; i++)
    {
        result[i] = prefix[i] * postfix[i];
    }

    return result;
}
```

### Solution 3 - Optimized Solution 2, compute prefix on output array
#### Beats: 66%
- Use the result array as the prefix values
- Calculate result at the same time with the postfix


```csharp
public int[] ProductExceptSelf(int[] nums)
{
	int n = nums.Length;
	var result = new int[n];

	// Calculate prefix of all elements to the left of each element
	result[0] = 1; // No elements to the left of the first element
	for (int i = 1; i < n; i++)
	{
		result[i] = result[i - 1] * nums[i - 1];
	}

	// Calculate products of all elements to the right and multiply with the postfix
	int postfix = 1;
	for (int i = n - 1; i >= 0; i--)
	{
		result[i] *= postfix;
		postfix *= nums[i];
	}

	return result;
}
```

# 242. Valid Anagram
## Difficulty: Easy
## Topics: Hash table, Sorting

### Solution 1 - Sort strings and check if they are equal
#### Beats: 30%
```csharp
public bool IsAnagram(string s, string t)
{
	if (s.Length != t.Length) return false;

	var sCharArray = s.ToCharArray();
	var tCharArray = t.ToCharArray();
	
	Array.Sort(sCharArray);
	Array.Sort(tCharArray);
	
	return sCharArray.SequenceEqual(tCharArray);
}
```

### Solution 2 - Frequency map
#### Beats: 80%
- Use a dictionary for the frequency map of the first string
- For the second
  - if char isn't found in freqMap => return false
  - decrement the count of that char and if it's lower than 0 => return false

```csharp
public bool IsAnagram(string s, string t)
{
	if (s.Length != t.Length) return false;
	
	Dictionary<char, int> freqMap = new();

	foreach (var c in s)
	{
		if (!freqMap.ContainsKey(c))
		{
			freqMap[c] = 0;
		}
		freqMap[c]++;
	}

	foreach (var c in t)
	{
		if (!freqMap.ContainsKey(c))
		{
			return false;
		}
		
		freqMap[c]--;

		if (freqMap[c] < 0)
		{
			return false;
		}
	}

	return true;
}
```

# 268. Missing Number
## Difficulty: Easy
## Topics: Hash Table, Binary Search, Sorting, Bit manipulation

### Solution 4 - XOR


### Solution 3 - Sequence Sum - Actual sum
- Use the difference between the theoretical sequence sum and the actual sum of the array to find the missing element

```csharp
public class Solution
{
    public int MissingNumber(int[] nums)
    {
        int n = nums.Length;

        int theoreticalSum = (n * (n + 1)) / 2;
        int realSum = nums.Sum();

        return theoreticalSum - realSum;
    }
}
```

### Solution 2 - Binary Search
- Something similar to solution 1, sort the array and use binary search
- If `nums[mid] == mid`, if midvalue is at the correct index it means that all values on the left are correct, continue searching the right side
- If `nums[mid] != mid`, if the value is not equal to the index then keep searching in the left zone to find the first element that has a wrong position

  - `0 1 2 3 4 5`
  - `0 1 2 3 5 6` 
  - `0 2 3 4 5 6`

```csharp
public class Solution
{
    public int MissingNumber(int[] nums)
    {
        int left = 0;
        int right = nums.Length - 1;
        
        Array.Sort(nums);

        while (left <= right)
        {
            int mid = (left + right) / 2;

            int midValue = nums[mid];

            // If midvalue is at the correct index it means that all values on the left are correct
            if (midValue == mid)
            {
                left = mid + 1;
            }
            // If the value is not equal to the index then keep searching in the left zone to find the first element that has a wrong position
            else
            {
                right = mid - 1;
            }
        }

        return left;
    }
}
```

### Solution 1 - Sort array
- Sort the array so that the number at index i needs to be equal to i
- The element that breaks this rule is the missing element 

```csharp
public class Solution
{
    public int MissingNumber(int[] nums)
    {
        // Sort the numbers so number at index i needs to be equal to i
        Array.Sort(nums);

        for (int i = 0; i < nums.Length; i++)
        {
            // If value at index i is different than i -> missing value
            // 0 1 2 3 4
            // 0 1 3 4 5
            if (nums[i] != i)
            {
                return i;
            }
        }

        return nums.Length;
    }
}
```

# 271. Encode and Decode Strings
## Difficulty: Medium
## Topics: Two Pointers, Sliding Window

### Solution 
- Encode the string by putting the word length and a delimiter before each word
- Decoding is done using 2 pointers
  - i is the start of the encoded word, at the first number that makes up the length
  - j is moved until # to find the word length, use substring and the calculated length to get the word
  - move i to the start of the next word length `j + 1 + wordLength`

```csharp
public string Encode(IList<string> strs)
{
	StringBuilder result = new StringBuilder();
	foreach (var str in strs)
	{
		result.Append(str.Length + "#" + str);
	}

	return result.ToString();
}

public List<string> Decode(string s)
{
	List<string> result = new List<string>();
	int i = 0;
	while (i < s.Length)
	{
		// find all numbers that make up the length of the word
		int j = i;
		while (j < s.Length && s[j] != '#')
		{
			j++;
		}

		// i is the start of the number, j is the end, use the length j-i to extract the length of the word
		int wordLength = Int32.Parse(s.Substring(i, j - i));

		// j is on the '#', j+1 is the first letter of the word
		string word = s.Substring(j + 1, wordLength);
		result.Add(word);

		// continue looking for new words after the last word that was found
		i = j + 1 + wordLength;
	}

	return result;
}
```

# 290. Word Pattern
## Difficulty: Easy
## Topics: Hash table
## Similar to: #205

### Solution 1 - 1 Dictionary
```csharp
public class Solution
{
    public bool WordPattern(string pattern, string s)
    {
        // pattern to word mapping dictionary
        var dictionary = new Dictionary<char, string>();
        var words = s.Split(' ');

        if (words.Length != pattern.Length) return false;

        for (int i = 0; i < pattern.Length; i++)
        {
            var currentWord = words[i];
            var currentPattern = pattern[i];

            if (!dictionary.TryGetValue(currentPattern, out var existingWordMapping))
            {
                // if mapping is different from existing mapping
                if (currentWord != existingWordMapping) return false;
                continue;
            }

            // if current word was already used for another pattern char
            if (dictionary.ContainsValue(currentWord)) return false;

            dictionary[currentPattern] = currentWord;
        }

        return true;
    }
}
```

### Solution 2 - 2 Dictionaries

```csharp
public class Solution
{
    public bool WordPattern(string pattern, string s)
    {
        var wordToPattern = new Dictionary<string, char>();
        var patternToWord = new Dictionary<char, string>();

        string[] words = s.Split(' ');

        if (words.Length != pattern.Length) return false;

        for (int i = 0; i < words.Length; i++)
        {
            var word = words[i];
            var patternChar = pattern[i];

            if (wordToPattern.ContainsKey(word))
            {
                if (wordToPattern[word] != patternChar) return false;
            }

            if (patternToWord.ContainsKey(patternChar))
            {
                if (patternToWord[patternChar] != word) return false;
            }

            if (!wordToPattern.ContainsKey(word) && !patternToWord.ContainsKey(patternChar))
            {
                wordToPattern[word] = patternChar;
                patternToWord[patternChar] = word;
            }
        }

        return true;
    }
}
```

# 344. Reverse String
## Difficulty: Easy
## Topics: Two pointers

### Solution - Two pointers, swap left and right chars
```csharp
public class Solution {
    public void ReverseString(char[] s)
    {
        int left = 0;
        int right = s.Length - 1;

        while (left < right)
        {
            (s[left], s[right]) = (s[right], s[left]);
            left++;
            right--;
        }
    }
}
```

# 347. Top K Frequent Elements
## Dificulty: Medium
## Topics: Hash Map, Heap

### Solution 1- Use a dictionary to store and sort frequencies
#### Beats: 38%
```csharp
public int[] TopKFrequent(int[] nums, int k)
{
	// Step 1: Create the frequency map
	var freqMap = new Dictionary<int, int>();
	foreach (var num in nums)
	{
		// If value doesn't exist in dictionary init it with 0
		freqMap.TryAdd(num, 0);

		// Increment the frequency
		freqMap[num]++;
	}

	// Step 2: Arrange the elements based on their frequency
	Dictionary<int, List<int>> buckets = new Dictionary<int, List<int>>();
	foreach (var num in freqMap.Keys)
	{
		int freq = freqMap[num];

		// If the frequency key doesn't exist in the dictionary, add a new list
		if (!buckets.ContainsKey(freq))
		{
			buckets[freq] = new List<int>();
		}

		// Add the number to the list for the corresponding frequency
		buckets[freq].Add(num);
	}

	var result = new List<int>();

	// Sort frequencies in descending order
	var sortedFrequencies = buckets.Keys.OrderByDescending(f => f).ToList();
	foreach (var freq in sortedFrequencies) {
		// Add all numbers with this frequency to the result
		result.AddRange(buckets[freq]);

		// Stop if we have enough elements
		if (result.Count >= k) {
			break;
		}
	}

	return result.Take(k).ToArray();
}
```
###
#### Beats: 42%
- Slightly faster due to sorting directly without the second dictionary
```csharp
public int[] TopKFrequent(int[] nums, int k)
{
    // Step 1: Create the frequency map
    var freqMap = new Dictionary<int, int>();
    foreach (var num in nums)
    {
        if (!freqMap.ContainsKey(num))
            freqMap[num] = 0;
        freqMap[num]++;
    }
    
    // Sort elements by frequency directly and take top k
    return freqMap.OrderByDescending(pair => pair.Value)
                  .Take(k)
                  .Select(pair => pair.Key)
                  .ToArray();
}
```


### Solution 2 - Use a minheap
#### Beats: 64%
- A dictionary is used to store the frequencies of the values
- A PriorityQueue / MinHeap is used to keep track of the top k most frequent elements
- For each key (number) in the frequency map:
	- The number is enqueued into the heap with its frequency as the priority
	- If the heap size exceeds k, the element with the smallest frequency is removed

```csharp
public int[] TopKFrequent(int[] nums, int k)
{
	// Step 1: Create the frequency map
	var freqMap = new Dictionary<int, int>();
	foreach (var num in nums)
	{
		// If value doesn't exist in dictionary init it with 0
		freqMap.TryAdd(num, 0);

		// Increment the frequency
		freqMap[num]++;
	}

	// Step 2: Use a minheap to keep the top k elements
	var minHeap = new PriorityQueue<int, int>();
	foreach (var key in freqMap.Keys)
	{
		// Enqueue the number with its frequency as the priority
		minHeap.Enqueue(key, freqMap[key]);

		// If the heap size exceeds k, remove the element with the smallest frequency
		if (minHeap.Count > k)
		{
			minHeap.Dequeue();
		}
	}

	var result = new List<int>();
	while (minHeap.Count > 0)
	{
		result.Add(minHeap.Dequeue());
	}

	return result.ToArray();
}
```

### Solution ? - Use Linq to group the numbers, sort desc, and take k elements
```csharp
public int[] TopKFrequent(int[] nums, int k)
{
	return nums
		.GroupBy(num => num)
		.OrderByDescending(num => num.Count())
		.Take(k)
		.Select(num => num.Key)
		.ToArray();
}
```

# 383. Ransom Note
## Difficulty: Easy
## Topics: Hash table, Counting

### Solution 1 - Char couting array
```csharp
public bool CanConstruct(string ransomNote, string magazine)
{
	if (ransomNote.Length > magazine.Length) return false;

	int[] charCounter = new int[26];

	foreach (var c in magazine)
	{
		charCounter[c - 'a']++;
	}

	foreach (var c in ransomNote)
	{
		if (charCounter[c - 'a'] == 0) return false;
		charCounter[c - 'a']--;
	}

	return true;
}
```

### Solution 2 - Dictionary for frequencies
```csharp
public bool CanConstruct(string ransomNote, string magazine)
{
	if(ransomNote.Length > magazine.Length) return false;

	// frequency for ransom note characters
	var ransomDict = new Dictionary<char, int>();
	foreach (var c in ransomNote)
	{
		if (!ransomDict.ContainsKey(c))
		{
			ransomDict[c] = 0;
		}
		ransomDict[c]++;
	}
	
	// frequency for magazine characters
	var magazineDict = new Dictionary<char, int>();
	foreach (var c in magazine)
	{
		magazineDict.TryAdd(c, 0);
		magazineDict[c]++;
	}

	foreach (var ransomPair in ransomDict)
	{
		// if magazine doesn't contain the ransom note key -> return false
		if (!magazineDict.TryGetValue(ransomPair.Key, out var magazineFrequency))
		{
			return false;
		}
		
		// if magazine char frequency is less than what is required for the ransom note -> return false
		if (magazineFrequency < ransomPair.Value)
		{
			return false;
		}
	}

	return true;
}
```

### Solution 2 - Counting array for chars

# 387. First Unique Character in a String
## Difficulty: Easy
## Topics: Hash Table, Queue, Counting

### Solution 1 - Count frequencies
```csharp
public class Solution
{
    public int FirstUniqChar(string s)
    {
        int[] freq = new int[26];
        foreach (var c in s)
        {
            freq[c - 'a']++;
        }

        for (int i = 0; i < s.Length; i++)
        {
            if (freq[s[i] - 'a'] == 1)
            {
                return i;
            }
        }

        return -1;
    }
}
```

# 392. Is Subsequence
## Difficulty: Easy
## Topics: Two Pointers, Dynamic Programming

### Solution 1 - Two Pointers
- Use 2 pointers both starting at 0, one for `s` and one for `t`
- In each iteration
  - check if `s[sIndex] == t[tIndex]` and increment `sIndex` to point on the next char if true
  - increment `tIndex`
- If there are missing chars in s, ex `acx` - `abcd`, `sIndex` will be less than `s.Length`
- 2 pointer method checks if chars appear in the same order `abc` - `acb` -> `sIndex` will be 1 -> `false` 

```csharp
public class Solution
{
    public bool IsSubsequence(string s, string t)
    {
        int sIndex = 0;
        int tIndex = 0;
        
        while (sIndex < s.Length && tIndex < t.Length)
        {
            if (s[sIndex] == t[tIndex])
            {
                sIndex++;
            }

            tIndex++;
        }

        return sIndex == s.Length;
    }
}
```

# 424. Longest Repeating Character Replacement
## Difficulty: Medium
## Topics: Sliding Window, Hash table

### Solution 1 - 
- The solution is the size of the biggest valid window
- A window is valid if the number of characters that arent the majority can be replaced using k chars, so the condition is `window.Legth - countOfMajorityElement <= k`
- If a window respects the condition that it can be counted for the max window length

- To solve this use a sliding window, left & right both start at 0
- In each iteration
  - Right is incremented
  - The current window is checked for validity
    - If the current window is valid then try to update max
	- If the current window is invalid then left gets incremented until the window becomes valid

``` csharp
public class Solution
{
    public int CharacterReplacement(string s, int k)
    {
        int maxValue = 0;
        
        int left = 0;
        int right = 0;
        var freqMap = new Dictionary<char, int>();
        // Track the maximum frequency in the current window
        int maxElementCount = 0; 

        while (right < s.Length)
        {
            char current = s[right];

            // If current character doesn't exist in the hashmap init it with 0
            freqMap.TryAdd(current, 0);
            
            // Update the frequency of the current character
            freqMap[current]++;

            // Update the maximum frequency in the current window
            maxElementCount = Math.Max(maxElementCount, freqMap[current]);

            // Calculate the length of the current window
            int windowLength = right - left + 1;

            // If the window is not valid, move the left pointer to the right
            if (windowLength - maxElementCount > k)
            {
                freqMap[s[left]]--;
                left++;
            }
            else
            {
                // Update the maximum length of the valid window
                maxValue = Math.Max(maxValue, windowLength);
            }

            right++;
        }
        
        return maxValue;
    }
}
```


# 451. Sort Characters By Frequency
## Difficulty: Medium
## Topics: Hash Map, Heap, Bucket Sort

### Solution 1 - Heap
#### Beats: 86%
- Use a dictionary to create the frequency map
- Use a maxHeap to enqueue the elements
- Pop each element to get the sorted chars in decreasing order of their frequencies

```csharp
public string FrequencySort(string s)
{
	Dictionary<char, int> frequencyMap = new();

	// create the frequency map with the char and its frequency
	foreach (var c in s)
	{
		if (!frequencyMap.ContainsKey(c))
		{
			frequencyMap[c] = 0;
		}

		frequencyMap[c]++;
	}

	// create the maxHeap, top contains the element with the highest frequency
	var maxHeapComparer = Comparer<int>.Create((x, y) => y.CompareTo(x));
	PriorityQueue<char, int> maxHeap = new(maxHeapComparer);

	//OR maxHeap.EnqueueRange(frequencyMap.Select(kvp => (kvp.Key, kvp.Value)));
	foreach (var kvp in frequencyMap)
	{
	    maxHeap.Enqueue(kvp.Key, kvp.Value);
	}

	// deque each element and create the string using the frequency / priority
	StringBuilder sb = new();
	while (maxHeap.TryDequeue(out char c, out int frequency))
	{
		for (int i = 0; i < frequency; i++)
		{
			sb.Append(c);
		}
	}

	return sb.ToString();
}
```

# 496. Next Greater Element I
## Difficulty: Easy
## Topics: Monotonic Stack, Hash Table

### Solution
- Use a monotonic stack to store the nums with no higher element
- When a higher element is found pop the nums from the stack until the stack becomes monotonic again (until the stack is empty or the current element is <= than the element in the stack)
- For each popped element put them in a dictionary with the popped value as the key and the num as the value [element, nextHighest]
- At the end loop over all values in num1 and seach the dictionary, if the element is contained then it has a nextHighest element, otherwise it doesn't

```csharp
public class Solution
{
    public int[] NextGreaterElement(int[] nums1, int[] nums2)
    {
        if (nums1.Length == 0) return [];

        var decreasingStack = new Stack<int>();
        var nextHighestDict = new Dictionary<int, int>();

        foreach (var num in nums2)
        {
            // If a bigger element than the one on top of the stack is found then keep popping the elements
            // and add them to a dictionary with [element, nextHighest]
            while (decreasingStack.Count > 0 && num > decreasingStack.Peek())
            {
                nextHighestDict.Add(decreasingStack.Pop(), num);
            }

            decreasingStack.Push(num);
        }

        // Construct the result array by looking for the array values in the dictionary
        int[] res = new int[nums1.Length];
        for (int i = 0; i < nums1.Length; i++)
        {
            // If dictionary doesn't contain the element then it has no element higher than it
            res[i] = nextHighestDict.ContainsKey(nums1[i])? nextHighestDict[nums1[i]] : -1;
        }

        return res;
    }
}
```

# 567. Permutation in String
## Difficulty: Medium
## Topics: Sliding Window, Hash table

### Solution 2 - 
```csharp
public class Solution {
    public bool CheckInclusion(string s1, string s2) {
        int len1 = s1.Length;
        int len2 = s2.Length;

        // If s1 is longer than s2, no permutation is possible
        if (len1 > len2) 
            return false;

        // Arrays to store the count of each character in s1 and the current window of s2
        int[] count1 = new int[26];
        int[] count2 = new int[26];

        // Initialize the character counts for s1 and the first window of s2
        for (int i = 0; i < len1; i++) {
            count1[s1[i] - 'a']++; // Increment count for s1
            count2[s2[i] - 'a']++; // Increment count for the first window of s2
        }

        // Check if the initial window is a valid permutation
        if (AreArraysEqual(count1, count2))
            return true;

        // Slide the window over s2
        for (int i = len1; i < len2; i++) {
            // Add the new character to the window
            count2[s2[i] - 'a']++;

            // Remove the leftmost character from the window
            count2[s2[i - len1] - 'a']--;

            // Check if the current window is a valid permutation
            if (AreArraysEqual(count1, count2))
                return true;
        }

        return false;
    }

    // Helper method to compare two integer arrays for equality
    private bool AreArraysEqual(int[] arr1, int[] arr2) {
        for (int i = 0; i < 26; i++) {
            if (arr1[i] != arr2[i]) {
                return false;
            }
        }
        return true;
    }
}
```

### Solution 1 - Compare frequency of characters for s1 and window
- Create a dictionary to store the frequencies of s1
- Make a window of size `s1.Length`, if the window was smaller - not all characters would fit, if it was bigger then wrong chars would be counted
- For each window use a dictionary to store the frequencies of the chars inside the window and then compare the keys and the frequencies with that of s1
- If they don't match then slide the window to the right `left++` and `right++`

```csharp
public class Solution
{
    public bool CheckInclusion(string s1, string s2)
    {
        // If s1 is bigger than s2, no permutation can exist
        if (s1.Length > s2.Length)
            return false;

        // Initialize a dictionary to store the character counts of s1
        var charCount = new Dictionary<char, int>();
        foreach (var c in s1)
        {
            charCount.TryAdd(c, 0);
            charCount[c]++;
        }

        int left = 0;
        // Window size is fixed to s1.Length
        int right = s1.Length; 

        while (right <= s2.Length) // Use <= to include the last window
        {
            // Create a temporary dictionary to count characters in the current window
            var windowCount = new Dictionary<char, int>();
            for (int i = left; i < right; i++)
            {
                char currentChar = s2[i];
                if (!charCount.ContainsKey(currentChar))
                    break; // If a character is not in s1, skip this window

                windowCount.TryAdd(currentChar, 0);
                windowCount[currentChar]++;
            }

            // Check if the windowCount matches charCount
            bool isMatch = true;
            foreach (var kvp in charCount)
            {
                if (!windowCount.ContainsKey(kvp.Key) || windowCount[kvp.Key] != kvp.Value)
                {
                    isMatch = false;
                    break;
                }
            }

            // If the window matches, return true
            if (isMatch)
                return true;

            // Move the window to the right
            left++;
            right++;
        }

        return false;
    }
}
```

# 594. Longest Harmonious Subsequence
## Difficulty: Easy
## Topics: Sliding Window, Hash Table, Counting, Sorting

### Solution 1 - Sliding Window
```csharp
public class Solution
{
    public int FindLHS(int[] nums)
    {
        int result = 0;

        // Sort the arrays - order of elements doesn't matter, now left element in window is the min and right is the max
        Array.Sort(nums);

        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            // If the difference is greater than 1, shrink window from left until difference is <= 1
            while (left < right && nums[right] - nums[left] > 1)
            {
                left++;
            }

            // If the difference is exactly 1, we have a valid harmonious subsequence
            if (nums[right] - nums[left] == 1)
            {
                result = Math.Max(result, right - left + 1);
            }
        }
        
        return result;
    }
}
```

# 643. Maximum Average Subarray I
## Difficulty: Easy
## Topics: Sliding Window

### Solution - Simple Sliding Window (Fixed)
```csharp
public class Solution
{
    public double FindMaxAverage(int[] nums, int k)
    {
        double max = double.MinValue;

        int currentSum = 0;
        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            currentSum += nums[right];

            if (right - left + 1 > k)
            {
                currentSum -= nums[left];
                left++;
            }

            if (right - left + 1 == k)
            {
                max = Math.Max(max, (double)currentSum / k);
            }
        }

        return max;
    }
}
```

# 682. Baseball Game
## Difficulty: Easy (Very)
## Topics: Stack

### Solution
```csharp
public class Solution
{
    public int CalPoints(string[] operations)
    {
        var stack = new Stack<int>();

        foreach (var op in operations)
        {
            if (op == "C")
            {
                stack.Pop();
            }
            else if (op == "D")
            {
                stack.Push(stack.Peek() * 2);
            }
            else if (op == "+")
            {
                var second = stack.Pop();
                var first = stack.Peek();
                stack.Push(second);
                stack.Push(first + second);
            }
            else
            {
                stack.Push(int.Parse(op));
            }
        }

        if (stack.Count == 0)
        {
            return 0;
        }

        int result = 0;
        while (stack.Count > 0)
        {
            result += stack.Pop();
        }

        return result;
    }
}
```

# 692. Top K Frequent Words
## Difficulty: Medium
## Topics: Hash map, Heap, Bucket sort, Sorting

### Solution 1 - Max heap with custom comparer
#### Beats: 85%
```csharp
public IList<string> TopKFrequent(string[] words, int k)
{
	// create the frequency map for the words
	Dictionary<string, int> frequencyMap = new();
	foreach (var word in words)
	{
		frequencyMap.TryAdd(word, 0);
		frequencyMap[word]++;
	}

	// use a minheap to keep just the k top words with the highest frequencies
	var maxHeapComparer = Comparer<(int Priority, string Value)>.Create((x, y) =>
	{
		// First, compare by priority in descending order
		int priorityComparison = y.Priority.CompareTo(x.Priority);
		if (priorityComparison != 0)
		{
			return priorityComparison;
		}

		// If priorities are the same, compare by string in ascending order
		return string.Compare(x.Value, y.Value, StringComparison.Ordinal);
	});

	PriorityQueue<string, (int Priority, string Value)> maxHeap = new(maxHeapComparer);
	foreach (var kvp in frequencyMap)
	{
		maxHeap.Enqueue(kvp.Key, (kvp.Value, kvp.Key));
	}

	int currentK = 0;
	List<string> result = new();
	while (currentK < k)
	{
		result.Add(maxHeap.Dequeue());
		currentK++;
	}

	return result;
}
```

# 704. Binary Search 
## Difficulty: Easy
## Topics: Binary Search
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
		// calcualate middle index
		int middle = (left + right) / 2;

		// check if target has been found
		if (nums[middle] == target)
		{
			return middle;
		}

		// if target is smaller than middle value keep searching in the left side of the array
		if (nums[middle] > target)
		{
			right = middle - 1;
		}
		// if targer is bigger than middle value keep searching in the right side of the array
		else
		{
			left = middle + 1;
		}
	}

	return -1;
}
```



# 739. Daily Temperatures
## Difficulty: Medium
## Topics: Monotonoic Stack

### Solution
- Use a monotonic stack to store the indeces of the temperatures
- If a higher temperature found then while there are smaller elements on the stack pop them and calculate the index difference

```csharp
public class Solution
{
    public int[] DailyTemperatures(int[] temperatures)
    {
        Stack<int> monotonicDecreasing = new();
        var result = new int[temperatures.Length];

        for (int i = 0; i < temperatures.Length; i++)
        {
            while (monotonicDecreasing.Count > 0 && temperatures[i] > temperatures[monotonicDecreasing.Peek()])
            {
                int index = monotonicDecreasing.Pop();
                result[index] = i - index;
            }

            monotonicDecreasing.Push(i);
        }

        return result;
    }
}
```

# 853. Car Fleet
## Difficulty: Medium
## Topics: Monotonic Stack, Sorting

### Solution 1 - Monotonic Stack
- By sorting the cars based on their starting positions, we can iterate from the one closest to the destination to the one furthest from it - this allows us to determine which cars will catch up to each other
- We can calculate the time `t` that it takes for each car to reach the destination `t = (target - position[i]) / speed[i]`
- When a faster car reaches a slower one - the speed of both becomes that of the slower car -> monotonic decreasing stack
- Return the size of the stack for the number of fleets

```csharp
public class Solution {
    public int CarFleet(int target, int[] position, int[] speed)
    {
        var stack = new Stack<double>();

		// Sort the cars by their starting position (ascending order) - start processing cars that are farther away, more chances to overtake
        Array.Sort(position, speed);

        for (int i = 0; i < position.Length; i++)
        {
			// Calculate the time it takes for the current car to reach the target
            var current = (double)(target - position[i]) / speed[i];

			// Check if the current car catches up to the car ahead of it
            // If the current car's time is less than or equal to the time of the car ahead, it means the current car will catch up and form a fleet with the car ahead
			// Pop the car that is ahead from the stack
            while (stack.Any() && stack.Peek() < current)
            {
                stack.Pop();
            }

			// Push the current car's time onto the stack
            stack.Push(current);
        }
		
        return stack.Count();
    }
}
```

# 875. Koko Eating Bananas
## Difficulty: Medium
## Topics: Binary search

### Solution 2 - Binary Search
- The minimum possible eating speed is 1, the maximum possible is the max pile size
- We can apply a binary search on the eating speed
  - if mid eating speed is too slow then increase eating speed by moving left pointer to the right side
  - if mid eating speed is too high then try decreasing it by moving right pointer to the left side

```csharp
public class Solution
{
    public int MinEatingSpeed(int[] piles, int h)
    {
        // The minimum possible eating speed is 1
        int left = 1;
        // The maximum possible eating speed is the maximum pile size
        int right = piles.Max();

        // Perform binary search to find the minimum eating speed
        while (left <= right)
        {
            int mid = (left + right) / 2;

            // Calculate the total hours needed for the current eating speed (mid)
            int totalHours = 0;
            foreach (int pile in piles)
            {
                totalHours += (int)Math.Ceiling((double)pile / mid);
            }

            // If the total hours is within the limit, try a smaller eating speed
            if (totalHours <= h)
            {
                right = mid - 1;
            }
            // Otherwise, increase the eating speed
            else
            {
                left = mid + 1;
            }
        }

        // When left == right, we've found the minimum eating speed
        return left;
    }
}
```

### Solution 1 - Brute force (time excedded)
```csharp
public class Solution
{
    public int MinEatingSpeed(int[] piles, int h)
    {
        int k = 1;
        while (true)
        {
            int timeSum = 0;
            bool valid = true;

            // Calculate the total time required for the current k
            for (int i = 0; i < piles.Length; i++)
            {
                int pileSize = piles[i];
                int time = (pileSize + k - 1) / k; // Equivalent to Math.Ceiling(pileSize / k)
                timeSum += time;

                // Early exit if the current k is invalid
                if (timeSum > h)
                {
                    valid = false;
                    break;
                }
            }

            // If the current k is valid, return it
            if (valid)
                return k;

            // Otherwise, increment k and try again
            k++;
        }
    }
}
```

# 876. Middle of the Linked List
## Difficulty: Easy
## Topics: Linked List, Two Pointers (Slow and Fast)

### Solution - Slow and Fast Pointers
```csharp
public class Solution
{
    public ListNode MiddleNode(ListNode head)
    {
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        
        return slow;
    }
}
```

# 973. K Closest Points to Origin
## Difficulty: Medium
## Topics: Heap, Sorting, Quickselect

### Solution 1 - Maxheap
- Use a maxHeap with the distance as the priority
- Dequeue the elements if heap count is > k

```csharp
public int[][] KClosest(int[][] points, int k)
{
	if (k == points.Length) return points;

	// Create a max heap using a custom comparer
	var maxHeapComparer = Comparer<int>.Create((a, b) => b.CompareTo(a));
	PriorityQueue<int[], int> maxHeap = new(maxHeapComparer);

	foreach (int[] point in points)
	{
		int distSquared = point[0] * point[0] + point[1] * point[1];
		maxHeap.Enqueue(point, distSquared);

		if (maxHeap.Count > k)
		{
			maxHeap.Dequeue();
		}
	}

	// Create result array
	int[][] result = new int[maxHeap.Count][];
	
	for (int i = 0; i < result.Length; i++)
	{
		result[i] = maxHeap.Dequeue();
	}

	return result;
}
```


# 1004. Max Consecutive Ones III
## Difficulty: Medium
## Topics: Sliding window (Dynamic)

### Solution 
- Store the numbers of zeros in the window
- While the window doesn't respect the condition, zeros <= k, shrink the window from the left
- Update the max value with the current windowSize because

```csharp
public class Solution
{
    public int LongestOnes(int[] nums, int k)
    {
        int max = -1;

        int zeros = 0;

        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            if (nums[right] == 0)
                zeros++;

            // While the condition is false, the number of zeros in the window is more than k
            // Move the left pointer to the right to try to remove the zeros
            while (zeros > k)
            {
                if (nums[left] == 0)
                    zeros--;
                left++;
            }
            
            // The window will always be valid because the while on top always makes it valid
            max = Math.Max(max, right - left + 1);
        }

        return max;
    }
}
```

# 1011. Capacity To Ship Packages Within D Days
## Difficulty: Medium
## Topics: Binary Search

### Solution
- Consider the ship transport weight as the binary search value
- Left value is the biggest weight of the packages (can't move the biggest package otherwise) and right value is the sum of all packages (move all packages in a day)
- For each capacity search calculate the number of days it would take to move the packages
  - Store the number of days needed to transport all packages `daysNeeded` and the `currentWeight` of the packages
  - For each package weight check if it can be transported with the previous packages, 
    - if yes add it to `currentWeight`
	- if not then increase `daysNeeded` and reset `currentWeight` because the previous packages have been delivered


```csharp
public class Solution
{
    public int ShipWithinDays(int[] weights, int days)
    {
        // Left is max weight of the array - minimum possible ship carry weight otherwise the heviest package can't be carried
        int left = -1;
        
        // Right is the sum of all weights - ship can transport all the items at once
        int right = 0;
        foreach (int weight in weights)
        {
            left = Math.Max(left, weight);
            right += weight;
        }

        while (left < right)
        {
            int currentCapacity = (left + right) / 2;
            
            // If ship can move more items at once store the weights in currWeight
            int daysNeeded = 1;
            int currWeight = 0;
            foreach (int weight in weights)
            {
                // If weight of the package is more than the capacity then jump to the next day and transport packages (reset currWeight)
                if (currWeight + weight > currentCapacity)
                {
                    daysNeeded++;
                    currWeight = 0;
                }

                // Add package weight to currentWeight
                currWeight += weight;
            }

            // If the ship needs more days to transport the packages than the limit then increase the capacity
            if (daysNeeded > days)
            {
                left = currentCapacity + 1;
            }
            else
            {
                right = currentCapacity;
            }
        }

        return left;
    }
}
```

# 1046. Last Stone Weight
## Difficulty: Easy
## Topics: Heap

### Solution 1 - Maxheap
```csharp
public class Solution
{
    public int LastStoneWeight(int[] stones)
    {
        var maxHeapComparer = Comparer<int>.Create((x, y) => y.CompareTo(x));
        var maxHeap = new PriorityQueue<int, int>(maxHeapComparer);

        foreach (var stone in stones)
        {
            maxHeap.Enqueue(stone, stone);
        }

        while (maxHeap.Count >= 2)
        {
            var stone1 = maxHeap.Dequeue();
            var stone2 = maxHeap.Dequeue();

            var newStone = stone1 - stone2;
            maxHeap.Enqueue(newStone, newStone);
        }

        return maxHeap.Dequeue();
    }
}
```

# 1248. Count Number of nice Subarrays
## Difficulty: Medium
## Topics: Prefix Sum, Sliding Window, Hash Table

### Solution 2 - Prefix sum ???
```csharp
public class Solution
{
    public int NumberOfSubarrays(int[] nums, int k)
    {
        int result = 0;

        // Convert all numbers to 0 or 1
        for (int i = 0; i < nums.Length; i++)
            nums[i] %= 2;

        // prefixCount[i] stores the count of prefix subarrays with sum i
        int[] prefixCount = new int[nums.Length + 1];
        prefixCount[0] = 1;
        int sum = 0;

        foreach (int num in nums)
        {
            sum += num;
            
            // If we have seen a prefix with sum (sum-k) before, it means the subarray between that prefix and current position has sum k
            // which means it contains exactly k odd numbers
            if (sum >= k)
            {
                result += prefixCount[sum - k];
            }

            // Increment the count of prefixes with current sum
            prefixCount[sum]++;
        }

        return result;
    }
}
```

### Solution 1 - Sliding Window: Count All subarrays of k and subtract all subarrays of size k-1
```csharp
public class Solution
{
    // Main method that returns count of subarrays with exactly k odd numbers
    // SubArray(nums, k) - SubArray(nums, k - 1) gives us the count of subarrays with exactly k odd numbers
    // SubArray(nums, k) counts all subarrays that have at most k odd numbers (0, 1, 2, ..., k odd numbers)
    // SubArray(nums, k-1) counts all subarrays that have at most (k-1) odd numbers (0, 1, 2, ..., k-1 odd numbers)
    public int NumberOfSubarrays(int[] nums, int k)
    {
        return Subarrays(nums, k) - Subarrays(nums, k - 1);
    }

    private int Subarrays(int[] nums, int k)
    {
        int result = 0;

        int oddNumbers = 0;
        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            int currentNum = nums[right];

            if (currentNum % 2 == 1)
                oddNumbers++;

            // Shrink window from left if odd count exceeds k
            while (oddNumbers > k)
            {
                // If element being removed is odd, decrement the count
                if (nums[left] % 2 == 1)
                    oddNumbers--;

                left++;
            }

            // At this point the window contains at most k odd numbers
            // All subarrays ending at 'end' and starting between 'start' and 'end' are valid
            // There are (end-start+1) such subarrays
            result += right - left + 1;
        }

        return result;
    }
}
```

# 1423. Maximum Points You Can Obtain from Cards
## Difficulty: Medium
## Topics: Sliding Window (Circular), Prefix Sum

### Solution 1 - Sliding the window from the first 3 elements circular
- Use a circular window that starts at the first 3 elements and moves in a circular way from the start of the array to the end
- Calculate the sum of the first 3 elements 0, 1 ,2
- Next, in each iteration move both pointers to the left in a circluar way: array of size 6, move 0, 1, 2 -> 0, 1, 5
  - Remove from the sum the right most element and add the new element: move 0, 1, 2 -> 0, 1, 5, remove `nums[2]` and add `nums[5]`

```csharp
public class Solution
{
    public int MaxScore(int[] cardPoints, int k)
    {
        int n = cardPoints.Length;

        // Find the sum of the first window made up of the element 0, 1 ,2
        int currentSum = 0;
        for (int i = 0; i < k; i++)
            currentSum += cardPoints[i];

        int maxScore = currentSum;

        // Now slide the window to the back using in a circular way
        for (int right = k - 1; right >= 0; right--)
        {
            // Remove the right most element from the current Sum
            currentSum -= cardPoints[right];
            
            // Calculate the index of the new leftmost element using in a circular rotation and add the value to the current sum
            int left = (right - k + n) % n;
            currentSum += cardPoints[left];

            maxScore = Math.Max(maxScore, currentSum);
        }

        return maxScore;
    }
}
```

# 1456. Maximum Number of Vowels in a Substring of Given Length
## Difficulty: Medium
## Topics: Sliding Window

### Solution 1 - Sliding Window
- Store the count of vowels
- In each iteration check if the current char is a vowel, if yes then increment the counter
- Check if the current window is bigger than `k`, if yes check if the first character in the window is a vowel, if yes then decrement the vowel counts
- At the end update the max count with the current vowel count

```csharp
public class Solution
{
    public int MaxVowels(string s, int k)
    {
        int res = 0;

        int vowels = 0;
        int left = 0;
        for (int right = 0; right < s.Length; right++)
        {
            if (IsVowel(s[right]))
            {
                vowels++;
            }

            if (right - left + 1 > k)
            {
                if (IsVowel(s[left]))
                {
                    vowels--;
                }

                left++;
            }

            if (right - left + 1 == k)
            {
                res = Math.Max(res, vowels);
            }
        }

        return res;
    }

    private bool IsVowel(char c)
    {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```

### Solution X - 
- Time limit excedded because of the list storing

```csharp
public class Solution
{
    public int MaxVowels(string s, int k)
    {
        int res = 0;

        var current = new List<char>();
        int left = 0;
        for (int right = 0; right < s.Length; right++)
        {
            current.Add(s[right]);

            if (right - left + 1 > k)
            {
                current.RemoveAt(0);
                left++;
            }

            if (right - left + 1 == k)
            {
                int temp = 0;
                foreach (var c in current)
                {
                    if (IsVowel(c))
                    {
                        temp++;
                    }
                }

                res = Math.Max(res, temp);
            }
        }

        return res;
    }

    private bool IsVowel(char c)
    {
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    }
}
```

# 1475. Final Prices With a Special Discount in a Shop
## Difficulty: Easy
## Topics: Two pointers, Monotonic stack

### Solution 1 - Two pointers (faster than monotonic stack)
- Loop over each element and calculate the discount with the first price lower than it
- Left is on the current price and right is moved from the next element until the end
  - If a number is found so prices[right] <= prices[left] then calcualte the discount

```csharp
public class Solution
{
    public int[] FinalPrices(int[] prices)
    {
        int[] discountedPrices = new int[prices.Length];

        for (int i = 0; i < prices.Length; i++)
        {
            discountedPrices[i] = prices[i];
            for (int j = i + 1; j < prices.Length; j++)
            {
                if (prices[j] <= prices[i])
                {
                    discountedPrices[i] -= prices[j];
                    break;
                }
            }
        }

        return discountedPrices;
    }
}
```

```csharp
public class Solution
{
    public int[] FinalPrices(int[] prices)
    {
        int[] discountedPrices = new int[prices.Length];
        
        int left = 0;
        while (left < prices.Length)
        {
            discountedPrices[left] = prices[left];
            
            int right = left + 1;
            while (right < prices.Length)
            {
                if (prices[right] <= prices[left])
                {
                    discountedPrices[left] -= prices[right];
                    break;
                }
                right++;
            }
            
            left++;
        }

        return discountedPrices;
    }
}
```

### Solution 2 - Increasint monotonic stack
- Use a monotonic stack to store the prices with no lower price for the discount
- When a lower price is found then pop the elements from the stack until the top of the stack is empty or it has >= value than the current price
- Store the popped elements in a dictionary with [index, discountPrice]

```csharp
public class Solution
{
    public int[] FinalPrices(int[] prices)
    {
        // Stack to store indices of prices
        var increasingStack = new Stack<int>(); 
        
        // Dictionary to store discounts for each index
        var discountDict = new Dictionary<int, int>(); 

        for (int i = 0; i < prices.Length; i++)
        {
            // While the stack is not empty and the current price is less than or equal to the price at the top of the stack
            while (increasingStack.Count > 0 && prices[i] <= prices[increasingStack.Peek()])
            {
                // Get the index of the previous price
                int prevIndex = increasingStack.Pop(); 
                
                // Store the discount for the previous index
                discountDict[prevIndex] = prices[i]; 
            }

            // Push the current index onto the stack
            increasingStack.Push(i); 
        }

        int[] discountedPrices = new int[prices.Length];
        for (int i = 0; i < prices.Length; i++)
        {
            // If the current index has a discount, subtract it from the price; otherwise, use the original price
            discountedPrices[i] = discountDict.ContainsKey(i) ? prices[i] - discountDict[i] : prices[i];
        }

        return discountedPrices;
    }
}
```

# 1652. Defuse the Bomb
## Difficulty: Easy
## Topics: Sliding Window

### Solution 2 - Sliding window

```csharp
public class Solution
{
    public int[] Decrypt(int[] code, int k)
    {
        int n = code.Length;
        int[] res = new int[n];

        if (k == 0) 
            return res;

        int left = 0;
        int curSum = 0;
        for (int right = 0; right < n + Math.Abs(k); right++)
        {
            // Add the new right element to the sum
            curSum += code[right % n];

            // If window size is too big, because of last right increase, then subtract last element from sum and move left
            if (right - left + 1 > Math.Abs(k))
            {
                curSum -= code[left % n];
                left = (left + 1) % n;
            }

            // If window size is equal to k
            if (right - left + 1 == Math.Abs(k))
            {
                // If k > 0 then the result needs to be written to the element previous to left
                if (k > 0)
                {
                    res[(left - 1 + n) % n] = curSum;
                }
                else
                {
                    res[(right + 1) % n] = curSum;
                }
            }
        }

        return res;
    }
}
```

### Solution 1 - Sum values
- Use % on the size of the array to loop around when index goes out of bounds

```csharp
public class Solution
{
    public int[] Decrypt(int[] code, int k)
    {
        int n = code.Length;
        int[] res = new int[n];

        // If k == 0 return the array with zeros
        if (k == 0)
            return res;

        for (int i = 0; i < n; i++)
        {
            // If k is more than 0 we need to replace with the sum of the k next elements
            if (k > 0)
            {
                // Start at position i until i + k
                for (int j = i + 1; j <= i + k; j++)
                {
                    // Use % to loop around the array when j goes out of bounds
                    // array of size 4: 0 1 2 3, if j is 4 => 0, 5 => 1
                    res[i] += code[j % n];
                }
            }
            else
            {
                // Start at position i - 1 until i - k 
                for (int j = i - 1; j >= i - Math.Abs(k); j--)
                {
                    // Use % to loop around the array
                    // Because j can be negative add n to it
                    // Adding n will not make it generate the wrong index, just make it so it's not negative 11 % 5 = 1; 16 % 5 = 1
                    int index = (j + n) % n;
                    res[i] += code[index];
                }
            }
        }

        return res;
    }
}
```

# 1768. Merge Strings Alternately
## Difficulty: Easy (Very)
## Topics: Two Pointer

### Solution 1 - One pointer for w1, one for w2
```csharp
using System.Text;

public class Solution {
    public string MergeAlternately(string word1, string word2)
    {
        var sBuilder = new StringBuilder();

        int w1Index = 0;
        int w2Index = 0;

        while (w1Index < word1.Length && w2Index < word2.Length)
        {
            sBuilder.Append(word1[w1Index]);
            sBuilder.Append(word2[w2Index]);
            
            w1Index++;
            w2Index++;
        }

        while (w1Index < word1.Length)
        {
            sBuilder.Append(word1[w1Index]);
            w1Index++;
        }
        
        while (w2Index < word2.Length)
        {
            sBuilder.Append(word2[w2Index]);
            w2Index++;
        }
        
        return sBuilder.ToString();
    }
}
```

# 1876. Substrings of Size Three with Distinct Characters
## Difficulty: Easy
## Topics: Sliding Window, Counting, Hash Table

### Solution 2 - Sliding Window

```csharp
public class Solution
{
    public int CountGoodSubstrings(string s)
    {
        int result = 0;

        var windowChars = new List<char>();
        int left = 0;
        int k = 3;

        for (int right = 0; right < s.Length; right++)
        {
            windowChars.Add(s[right]);

            // If window length is more than k remove the last element nad move left
            if (right - left + 1 > k)
            {
                windowChars.RemoveAt(0);
                left++;
            }

            // If the window length is equal to the target size check if all chars are unique
            if (right - left + 1 == k)
            {
                if (windowChars.Distinct().Count() == k)
                {
                    result++;
                }
            }
        }

        return result;
    }
}
```

### Solution 1 - Brute force
- 

```csharp
public class Solution
{
    public int CountGoodSubstrings(string s)
    {
        int res = 0;

        for (int i = 1; i < s.Length - 1; i++)
        {
            if (s[i] != s[i - 1] && s[i] != s[i + 1] && s[i - 1] != s[i + 1])
                res++;
        }

        return res;
    }
}
```


# 2024. Maximize the Confusion of an Exam
## Difficulty: Medium
## Topics: Sliding Window

### Solution - Sliding Window
- In each iteration count the number of true and false answers in the winow
- While the condition is not satisfied then decrese window size by moving left to the right

```csharp
public class Solution
{
    public int MaxConsecutiveAnswers(string answerKey, int k)
    {
        int result = 0;
        int n = answerKey.Length;

        int trueCount = 0;
        int falseCount = 0;
        int left = 0;
        for (int right = 0; right < n; right++)
        {
            char current = answerKey[right];

            // Add current char to the window
            if (current == 'T')
                trueCount++;
            else
                falseCount++;

            // Check validity condition
            // While either trueCount or falseCount is bigger than k then reduce window size by moving left to the right
            while (trueCount > k && falseCount > k)
            {
                if (answerKey[left] == 'T')
                    trueCount--;
                else
                    falseCount--;

                left++;
            }

            // Result is the window size
            result = Math.Max(result, right - left + 1);
        }

        return result;
    }
}
```

# 2089. Find Target Indices After Sorting Array
## Difficulty: Easy
## Topics: Binary Search, Sorting

### Solution 2 - Binary Search
- Use binary search to find a target element
- Move mid until the first appearance of that element in the array
- Add indeces from the duplicate array

```csharp
public class Solution
{
    public IList<int> TargetIndices(int[] nums, int target)
    {
        var result = new List<int>();
        
        Array.Sort(nums);

        int left = 0;
        int right = nums.Length - 1;

        while (left <= right)
        {
            int mid = (left + right) / 2;
            int midVal = nums[mid];

            // Find the index of the target
            if (midVal == target)
            {
                // Move `mid` to the first occurrence of the target
                while (mid > 0 && nums[mid - 1] == target)
                {
                    mid--;
                }

                // Add all occurrences of the target starting from the first occurrence
                while (mid < nums.Length && nums[mid] == target)
                {
                    result.Add(mid);
                    mid++;
                }

                break;
            }

            if (midVal >= target)
            {
                right = mid - 1;
            }

            if (midVal < target)
            {
                left = mid + 1;
            }
        }

        return result;
    }
}
```

### Solution 1 - Queue
- Put the indeces of target values in a queue, return the queue at the end

```csharp
public class Solution {
    public IList<int> TargetIndices(int[] nums, int target) {
        Array.Sort(nums);
        
        var queue = new Queue<int>();
        for (int i = 0; i < nums.Length; i++)
        {
            if (nums[i] == target)
            {
                queue.Enqueue(i);
            }
        }
        
        
        return queue.ToArray().ToList();
    }
}
```

# 2379. Minimum Recolors to Get K Consecutive Black Blocks
## Difficulty: Easy
## Topics: Sliding Window

### Solution - Sliding Window
- Store the count of black characters
- In each iteration if new character is B then increment the count
- If window size is too big then move left pointer to the right and decrement black count if the character that was removed is B
- If window size is k then calculate the min replace as `windowSize - blackCount`

```csharp
public class Solution
{
    public int MinimumRecolors(string blocks, int k)
    {
        int result = Int32.MaxValue;

        int blackCount = 0;

        int left = 0;
        for (int right = 0; right < blocks.Length; right++)
        {
            if (blocks[right] == 'B')
                blackCount++;

            if (right - left + 1 > k)
            {
                if (blocks[left] == 'B')
                    blackCount--;
                left++;
            }

            if (right - left + 1 == k)
            {
                int replace = right - left + 1 - blackCount;
                result = Math.Min(result, replace);
            }
        }

        return result;
    }
}
```

# 2404. Most Frequent Even Element
## Difficulty: Easy
## Topics: Hash table, Sorting, Heap

### Solution 1 - Dictionary with frequencies
- Use a dictionary to find frequencies of each num
- Loop over all the kvps in the dictionary and update the element

```csharp
public int MostFrequentEven(int[] nums)
{
	Dictionary<int, int> freqMap = new Dictionary<int, int>();

	foreach (var num in nums)
	{
		if (num % 2 == 0)
		{
			if (!freqMap.ContainsKey(num))
			{
				freqMap[num] = 0;
			}

			freqMap[num]++;
		}
	}
	
	if (freqMap.Count == 0)
	{
		return -1;
	}
	
	(int mostFrequentEven, int maxFrequency) = (-1, -1);

	foreach (var kvp in freqMap)
	{
		if (kvp.Value > maxFrequency || (kvp.Value == maxFrequency && kvp.Key < mostFrequentEven))
		{
			mostFrequentEven = kvp.Key;
			maxFrequency = kvp.Value;
		}
	}
	
	return mostFrequentEven;
}
```


# 2500. Delete Greatest Value in Each Row
## Difficulty: Easy (Medium)
## Topics: Sorting

### Solution 1 - Sort the rows, find max for each column and add it to sum
- In each iteration the maximum value of each row is added to the sum
- By sorting the rows at the start the maximum values of each array for a iteration will be placed on the same column
- Find the max of each column and add it to the sum

```csharp
public class Solution
{
    public int DeleteGreatestValue(int[][] grid)
    {
        // Sort the rows, now smallest values are at the start, bigger ones are on the right
        foreach (int[] row in grid)
        {
            Array.Sort(row);
        }

        // Iterate over the matrix in column direction, the max element in each column will be added to the sum
        int result = 0;
        for (int i = 0; i < grid[0].Length; i++)
        {
            int max = 0;
            for (int j = 0; j < grid.Length; j++)
            {
                max = Math.Max(max, grid[j][i]);
            }

            result += max;
        }

        return result;
    }
}
```

# 2529. Maximum Count of Positive Integer and Negative Integer
## Difficulty: Easy / Medium for binary search
## Topics: Binary Search, Counting

### Solution 2
- Use binary search to the index of the last negative number and the index of the first positive number
- Use them to calculate the number of zeros and the count of positive and negative numbers

```csharp
public class Solution
{
    public int MaximumCount(int[] nums)
    {
        int lastNegativeIndex = LastNegativeIndex(nums);
        int firstPositiveIndex = FirstPositiveIndex(nums);

        // Calculate the count of negative numbers
        int negativeCount = lastNegativeIndex + 1;

        // Calculate the count of positive numbers
        int positiveCount = nums.Length - firstPositiveIndex;

        // Return the maximum of the two counts
        return Math.Max(negativeCount, positiveCount);
    }

    private int FirstPositiveIndex(int[] nums)
    {
        int left = 0;
        int right = nums.Length - 1;

        while (left <= right)
        {
            int mid = left + (right - left) / 2;

            // If mid value is positive, and it is either the first element of the array or the element before it was negative
            if (nums[mid] > 0 && (mid == 0 || nums[mid - 1] <= 0))
            {
                // Found the first positive number
                return mid;
            }
            else if (nums[mid] > 0)
            {
                // Search in the left half
                right = mid - 1;
            }
            else
            {
                // Search in the right half
                left = mid + 1;
            }
        }

        // If no positive number is found, return nums.Length
        return nums.Length;
    }

    private int LastNegativeIndex(int[] nums)
    {
        int left = 0;
        int right = nums.Length - 1;

        while (left <= right)
        {
            int mid = left + (right - left) / 2;

            // If the mid value is negative and it's either the last element of the array or the element after it is positive
            if (nums[mid] < 0 && (mid == nums.Length - 1 || nums[mid + 1] >= 0))
            {
                // Found the last negative number
                return mid;
            }
            else if (nums[mid] < 0)
            {
                // Search in the right half
                left = mid + 1;
            }
            else
            {
                // Search in the left half
                right = mid - 1;
            }
        }

        // If no negative number is found, return -1
        return -1;
    }
}
```

### Solution 1 - Brute force, look at all elements

# 2540. Minimum Common Value
## Difficulty: Easy
## Topics: Binary Search, Two Pointers

### Solution 1 - Two pointers
- Use 2 pointers one for nums1 and one for nums2
- If the values at the pointers are equal then the common minimum has been found
- If num1Value is bigger than nums2Value then nums2 index needs to move to the right so it can find bigger value
- Same for nums2Value

```csharp
public class Solution {
    public int GetCommon(int[] nums1, int[] nums2)
    {
        int idx1 = 0;
        int idx2 = 0;
        
        while (idx1 < nums1.Length && idx2 < nums2.Length)
        {
            int nums1Val = nums1[idx1];
            int nums2Val = nums2[idx2];

            if (nums1Val == nums2Val)
            {
                return nums1Val;
            }

            if (nums1Val < nums2Val)
            {
                idx1++;
            }
            else
            {
                idx2++;
            }
        }

        return -1;
    }
}
```

# 2558. Take Gifts From the Richest Pile
## Difficulty: Easy
## Topics: Heap

### Solution 1 - Heap
```csharp
public class Solution
{
    public long PickGifts(int[] gifts, int k)
    {
        var maxHeap = new PriorityQueue<int, int>(Comparer<int>.Create((x, y) => y.CompareTo(x)));

        foreach (var elem in gifts)
        {
            maxHeap.Enqueue(elem, elem);
        }

        for (int i = 0; i < k; i++)
        {
            long val = maxHeap.Dequeue();
            int changedVal = (int)Math.Floor(Math.Sqrt(val));
            maxHeap.Enqueue(changedVal, changedVal);
        }

        long result = 0;
        while (maxHeap.Count > 0)
        {
            result += maxHeap.Dequeue();
        }

        return result;
    }
}
```

# 2594. Minimum Time to Repair Cars
## Difficulty: Medium
## Topics: Binary Search

### Solution 
- Apply a binary search on the time that each engineer takes to repair a car
- Maximum possible value for the binary search is the time that the lowest rank takes to repair every car

```csharp
public class Solution
{
    public long RepairCars(int[] ranks, int cars)
    {
        // Min time to repair
        long  left = 0;

        // Max time to repair all the cars
        // Given by considering if the lowest rank tries to repair all the cars
        long  right = 1L * ranks.Min() * cars * cars;

        while (left <= right)
        {
            long  mid = (left + right) / 2;

            // time = r * c^2 => cars repaired in a time t are sqrt( time / r)
            long repairedCars = 0;
            foreach (var rank in ranks)
                repairedCars += (long)Math.Sqrt(1.0d * mid / rank);                

            // If the total cars that could be repaired in time frame is less than target
            // Then there is not enough time to repair the cars - increase time needed to repair
            if (repairedCars < cars)
                left = mid + 1;

            // If the total cars that could be repaired in time frame is greater than target
            // Then there is too much time to repair the cars - try to reduce time taken to repair
            if (repairedCars >= cars)
                right = mid - 1;
        }

        return left;
    }
}
```

# 2974. Minimum Number Game
## Difficulty: Easy
## Topics: Heap, Sorting

### Solution 2 - Sort the array and each iteration take a pair
```csharp
public class Solution
{
    public int[] NumberGame(int[] nums)
    {
        Array.Sort(nums);

        int index1 = 0;
        int index2 = 1;

        int[] result = new int[nums.Length];
        while (index2 < nums.Length)
        {
            result[index1] = nums[index2];
            result[index2] = nums[index1];

            index1 += 2;
            index2 += 2;
        }
        
        return result;
    }
}
```

### Solution 1 - Use a minheap to store the numbers

```csharp
public class Solution
{
    public int[] NumberGame(int[] nums)
    {
        var minHeap = new PriorityQueue<int, int>();

        foreach (var elem in nums)
        {
            minHeap.Enqueue(elem, elem);
        }

        int[] result = new int[nums.Length];
        int index = 0;

        while (minHeap.Count > 0)
        {
            int alice = minHeap.Dequeue();
            int bob = minHeap.Dequeue();

            result[index++] = bob;
            result[index++] = alice;
        }

        return result;
    }
}
```

# 3258. Count Substrings That Satisfy K-Constraint I
## Difficulty: Easy
## Topics: Sliding Window


### Solution 2 - Sliding Window
- We maintain a sliding window that tracks the number of 0s and 1s in the current substring. As we iterate through the string, we expand the window by including the current character. If the counts of 0s and 1s both exceed k, we adjust the left boundary of the window until it becomes valid again. For each valid window, we count the number of substrings ending at the current character.


```csharp
public class Solution
{
    public int CountKConstraintSubstrings(string s, int k)
    {
        int result = 0;

        int zeros = 0;
        int ones = 0;

        int left = 0;
        for (int right = 0; right < s.Length; right++)
        {
            char current = s[right];
            if (current == '0')
                zeros++;
            else
                ones++;

            // If both conditions are false need to shrink the window
            while (!(zeros <= k || ones <= k))
            {
                // Remove the first char in the window and update the counts
                if (s[left] == '0')
                    zeros--;
                else
                    ones--;

                left++;
            }

            // All substrings ending at r and starting from l to r are valid
            result += (right - left + 1);
        }

        return result;
    }
}
```

### Solution 1 - Brute force

```csharp
public class Solution {    
    public int CountKConstraintSubstrings(string s, int k) {
        int result = 0;

        for (int i = 0; i < s.Length; i++)
        {
            int zeros = 0;
            int ones = 0;
            for (int j = i; j < s.Length; j++)
            {
                if (s[j] == '0')
                {
                    zeros++;
                }
                else
                {
                    ones++;
                }
                if (zeros <= k || ones <= k)
                {
                    result++;
                }
            }
        }

        return result;
    }
}
```



# Geeks For Geeks

## Maximum sum of subarray - Geeks for geeks

### Solution 2 - Sliding Window (Static Size)

```java
public static int maximumSumSubarray(int[] arr, int k) {
    // Check if array has sufficient elements
    if (arr.length < k)
        return -1;

    int max = 0;

    int currentSum = 0;
    int left = 0;
    for (int right = 0; right < arr.length; right++) {
        currentSum += arr[right];

        if (right - left + 1 > k) {
            currentSum -= arr[left];
            left++;
        }

        if (right - left + 1 == k) {
            max = Math.max(max, currentSum);
        }
    }

    return max;
}
```

### Solution 1 - Inneficient / No sliding window

```java
public int maximumSumSubarray(int[] arr, int k) {
    // Check if array has sufficient elements
    if (arr.length < k) {
        return -1;
    }

    int max = Integer.MIN_VALUE;

    for (int right = k - 1; right < arr.length; right++) {
        int currentSum = 0;
        for (int left = right - k + 1; left <= right; left++) {
            currentSum += arr[left];
        }
        max = Math.max(max, currentSum);
    }

    return max;
}
```

## Longest Subarray With Sum < S

### Solution - Sliding Window (Dynamic Size)
```csharp
    public int LongestSubarrayWithSumLess(int[] nums, int s)
    {
        int result = 0;

        int currentSum = 0;
        int left = 0;
        for (int right = 0; right < nums.Length; right++)
        {
            currentSum += nums[right];

            if (currentSum < s)
            {
                currentSum -= nums[left];
                left++;
            }

            result = Math.Max(result, currentSum);
        }

        return result;
    }
```