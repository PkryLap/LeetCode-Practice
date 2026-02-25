# LeetCode Day 1

## 1.Two Sum

### 给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值** *`target`*  的那两个整数，并返回它们的数组下标。每种输入只会对应一个答案，并且你不能使用两次相同的元素。



### 思路1: 暴力循环

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0 ; i < nums.size() ; i++){
            for (int j = i+1 ; j < nums.size() ; j++){
                if (nums[i] + nums[j] == target)return{i,j};
            }
        }
        return {};
    }
};
```


