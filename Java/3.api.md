# 数学Math （随机数）
Math 类的 random()的调用，会返回一个[0,1)范围的一个 double 型值
`Math.random()`

# 数组Arrays

```java
import java.util.Arrays;
public class SortTest {
	public static void main(String[] args) {
		int[] arr = {3, 2, 5, 1, 6}; 
		System.out.println("排序前" + Arrays.toString(arr));
		Arrays.sort(arr);
		System.out.println("排序后" + Arrays.toString(arr));
	}
}
```
