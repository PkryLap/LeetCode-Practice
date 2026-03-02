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

## 217. Contains Duplicate

### 给你一个整数数组 `nums` 。如果任一值在数组中出现 **至少两次** ，返回 `true` ；如果数组中每个元素互不相同，返回 `false` 。

### 思路1 ： 哈希表

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_map <int,int> mp;
        for (int i = 0 ; i < nums.size() ; i++){
            if (mp.find(nums[i]) != mp.end())return true;
            mp[nums[i]] = i;
        }
        return false;
    }
};
```

### 思路2 ：排序

```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        sort (nums.begin() , nums.end());
        for (int i = 0 ; i < nums.size()-1 ; i++) if(nums[i] == nums[i+1]) return true;
        return false;
    }
};
```

**有时候理论复杂度更优的哈希表，在实际运行中不一定比排序快，因为有内存分配的开销。**

## 49.**Group Anagrams**

### 给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

**示例 1:**

**输入:** strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

**输出:** [["bat"],["nat","tan"],["ate","eat","tea"]]

**解释：**

- 在 strs 中没有字符串可以通过重新排列来形成 `"bat"`。
- 字符串 `"nat"` 和 `"tan"` 是字母异位词，因为它们可以重新排列以形成彼此。
- 字符串 `"ate"` ，`"eat"` 和 `"tea"` 是字母异位词，因为它们可以重新排列以形成彼此。

### 思路1 ： 将每个字符串都内部排序，异位的会成为相同的

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map <string , vector <string> > mp;
        for (string s : strs){
            string t = s;
            sort(t.begin() , t.end());
            mp[t].push_back(s);
        }
        vector <vector <string> > ans;
        for (auto it = mp.begin() ; it != mp.end() ; it++){
            ans.push_back(it -> second);
        }
        return ans;
    }
};
```

### 思路2 ： 比如 "aab" 记为：a:2, b:1, 其他:0。 把这个计数特征转成字符串或者自定义结构作为 Key

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> mp;
        
        for (const string& s : strs) {
            // 1. Create a frequency array for 26 lowercase letters
            int count[26] = {0}; // Fixed size
            for (char c : s) {
                count[c - 'a']++; // Map 'a'->0, 'b'->1, etc.
            }
            
            // 2. Convert the frequency array to a string key
            // For example: "1#0#1#..." represents 1 'a', 0 'b', 1 'c'...
            string key = "";
            for (int i = 0; i < 26; i++) {
                key += "#"; // Delimiter to separate counts
                key += string(count[i]);
            }
            //diffenetiate 1#11 & 11#1
            // 3. Group by this unique "fingerprint"
            mp[key].push_back(s);
        }
        
        // 4. Collect results (Traversal)
        vector<vector<string>> res;
        for (auto& [key, value] : mp) {
            res.push_back(value);
        }
        return res;
    }
};
```

## map & vector

### traverse vector ：

```cpp
vector <int> nums = {1,2,3};
for (int x : nums){
    cout << x << " ";//directly reach the items in vector
}
```

```cpp
for (int i = 0 ; i < nums.size() ; i++){
    cout << nums[i] << " "; 
}
```

```cpp
for (auto it = nums.begin() ; it != nums.end(); it++){
    cout << *it << " "; // it functions as a pointer（实际上是迭代器）
}
```

### traverse map :

```cpp
unordered_map <string,int> mp = {{"apple",1}, {"banana",2}};
for (auto& [key, value] : mp) {
    cout << key << " 有 " << value << " 个" << endl;
}
//unordered_map会先输出banana后输出Apple他是按照哈希表的计算方式，map就会按照储存的顺序，先输出apple后输出banana
```

```cpp
for (auto &p : mp){
    cout << p.first << ":" << p.second;
}
/*为什么用 auto& 而不是 auto？

auto：会把容器里的东西复制一份，如果单词很多，复制会很慢。

auto&：是引用，直接指向原来的位置，不产生复制，速度极快。*/
```

```cpp
for (auto it = mp.begin() ; it != mp.end() ; it++){
    cout << it->first << ":" << it->second;
}
```

**push_back : vector 有，map没有**

**insert : 两者都有**

**vector的push_back 是直接加到最后，insert是插入在中间**

**map.insert({a,b})（map都是一对一对的形式）**

**Vector vs. Array: The Evolution of Contiguous Storage.**

相同点 (The Similarities)

- **连续内存 (Contiguous Memory)**：
  
  - 两者在内存中都是紧挨着排列的。
  
  - 这意为着它们都支持 **随机访问 (Random Access)**，你用 `nums[i]` 访问元素的速度都是 O(1)。

- **底层结构**：`vector` 的底层其实就是一个普通的 **C-style Array**。

不同点（differences）

    the size of array is fixed while the size of vector is dynamic (vector.push_back()) 

    vector.size()  vector.capacity()  valiable while array don't
