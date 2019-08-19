# Two Sum

[LeetCode](<https://leetcode.com/problems/two-sum/>)

给一个数组和目标值，当数组中的两个值相加为目标值，返回数组中两个值的下标。

## Example

give nums=[2,7,11,15];target=9;

因为 nums[0]+nums[1]=9；

return [0,1]。

## Approach 1：brute force

~~~java
class Solution{
	public int[] twoSum(int[] nums,int target){
		for(int i=0;i<nums.length;i++){
            for(int j=i+1;j<nums.length;j++){
                if(nums[i]==target-nums[j]){
                    return new int[]{i,j};
                }
            }
        }
        throw new IllegalArgumentException("no two sum exception");
	}
}
/*
time complexity:O(n平方)
space complexity：O(1)
*/
~~~



为了对时间复杂度进行优化，进行如下两种方法：

>我们需要一种更有效的方法来检查数组中是否存在目标元素。如果存在，我们需要找出它的索引。保持数组中的每个元素与其索引相互对应的最好方法是什么？哈希表

## Approach 2：Two-pass Hash Table

两遍哈希表

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map =new HashMap<>();
        for(int i=0;i<nums.length;i++){
            map.put(nums[i],i);
        }
        
        for(int j=0;j<nums.length;j++){
            int complement = target-nums[j];
            if(map.containsKey(complement) && map.get(complement)!=j){
                return new int[]{j,map.get(complement)};
            }
        }
        throw new IllegalArgumentException("no two sum exception");
    }
}

/*
time complexity：O(n)
space complexity:O(n)
*/
~~~

## Approach 3：One-pass Hash Table

一遍哈希表

~~~java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> map =new HashMap<>();      
        for(int j=0;j<nums.length;j++){
            int complement = target-nums[j];
            if(map.containsKey(complement)) {
                return new int[]{map.get(complement),j};
            }
            map.put(nums[j],j);
        }
        throw new IllegalArgumentException("no two sum exception");
    }
}
/*
也都是O(n)
*/
~~~

