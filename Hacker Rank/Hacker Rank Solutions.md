
# 1. Daily Temperatures
## Diff: Medium | Tags: Monotonic Stack, Stack
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
