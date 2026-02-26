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

### 思路2: 哈希表(我们用map储存数字k的位置i，然后进行寻找，找不到的就继续储存)

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map <int,int> mp;
        for (int i = 0 ; i < nums.size() ; i++){
            int com = target - nums[i];
            if (mp.find(com) != mp.end())return {mp[com] , i};
            mp[nums[i]] = i;
        }
        return {};
    }
};
```

**`count` vs `find` 进阶笔记**：

- **`count(key)`**：返回次数。在 `unordered_map` 中只有 0 或 1。优点是代码短，缺点是如果接下来要用这个值，会造成**二次查找**（查找成本翻倍）。

- **`find(key)`**：返回迭代器。如果找到了，迭代器里已经包含了值（`it->second`）。优点是**一次查找**搞定，性能最强。

- **第一次查找（执行 `mp.count(com)`）**：
  
  1. 计算机拿到 `com` 这个数字。
  
  2. 通过哈希函数计算出它的“储物柜”编号（下标）。
  
  3. 跑到那个下标对应的位置。
  
  4. **检查**那个位置有没有 `com` 这个数字。
  
  5. 返回结果（0 或 1）。

- **第二次查找（执行 `mp[com]`）**：
  
  1. 计算机又拿到了 `com` 这个数字。
  
  2. **重新**通过哈希函数计算一次它的“储物柜”编号。
  
  3. **重新**跑到那个下标对应的位置。
  
  4. **取出**存储在这个位置的 `Value`（即下标 `i`）。

```cpp
auto it = mp.find(com);
if (it != mp.end()) {
 return {it->second, i}; // it->second 就是存好的下标
}
```

//hi
