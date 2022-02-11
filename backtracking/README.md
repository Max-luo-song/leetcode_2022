## 1.回溯算法有如下几个分类，每类有处理的模板
1）组合问题：N个数中按一定规律找出k个数的组合
2）字符分割问题：字符串按照一定规则有几种切割方式
3）子集问题：N个数的集合中有多少符合条件的子集
4）排列问题：N个数按一定规则全排列
5）棋盘问题
## 2.回溯法的模板如下：
void backtracking(参数) {
	if (终止条件) {
		输出结果;
		return ;
	}
	for (选择:本层集合元素) {
		处理节点;
		backtracking(下一层参数); // 递归
		回溯，撤销处理结果
	}
}
回溯法有几个要注意的点：
1）确定backtracking的参数，应该包括操作数组+其他内容，要注意是否是引用
2）返回值的确定，大部分是void。但对于从所有可能里选出一个满足条件的要使用bool做返回值
3）终止条件的确定，一般是题目中某个可以量化的数字要求(总数达到xxx时)
## 3.组合问题
组合问题分类如下：
####3.1在一个集合中选取元素进行组合(用startIndex)
	3.1.1集合中没有重复元素
	3.1.2集合中有重复元素
####3.2在多个集合中选取元素进行组合(不用startIndex)
3.1.1 对于没有重复元素，要使用startIndex作为对下一层的介入，而且如果每个元素只能使用一次，下一层要加一进入。
模板如下(无重复元素+只能使用一次，详见77，216)：
void backtracking(int startIndex, int 操作数组) {
	终止条件
	for (int i=startIndex; i<n; i++) { // 对于组合问题i都是从startIndex开始，排列问题从0开始
		// 处理节点
		backtracking(i+1, 操作数组); // +1保证了字符不重复使用
		// 回溯
	}
}
模板如下(无重复元素+可以使用多次，详见39)：
void backtracking(int startIndex, int 操作数组) {
	终止条件
	for (int i=startIndex; i<n; i++) {
		// 处理节点
		backtracking(i, 操作数组); // 此处不加一保证重用
		// 回溯
	}
}
3.1.2 集合中有重复元素--要用used数组保证不重用
模板如下(有重复元素，只能使用一次，详见40)：
sort(nums.begin(), nums.end()); // 使用used数组必须要进行排序
void backtracking(int 操作数组nums，int startIndex, vector<bool>& used) {
	终止条件
	for (i=startIndex;i<n;i++) {
		// used[i - 1] == true，说明同一树枝candidates[i - 1]使用过,used[i - 1] == false，说明同一树层candidates[i - 1]使用过
		if (i>0 && nums[i-1]==nums[i] && used[i-1]==false)
			continue;
		// 处理节点
		used[i] = true;
		backtracking(i+1,操作数组,used)
		used[i] = false;
		// 回溯
	}
}
3.2 在多个集合中，区别在于找好映射。整体模板不变，for循环内控制条件还是一层可以选择范围。17中对应的就是每个digits代表的三个元素。要生成新的digits操作的对象。题中letter就是控制对象
## 4.字符分割问题
#### 4.1字符分割成无限份——区别在于终止条件不同
模板如下(详见131)：
void backtracking(string s, int startIndex) {
	// 终止条件：startIndex >= s.length()
	for (int i=startIndex; i<s.length(); i++) {
		// 处理节点
		backtracking(s, i+1);
		// 回溯
	}
}
#### 4.2字符分割成固定份数
模板如下(详见93)：
void backtracking(string s, int startIndex, int 控制分段份数变量) {
	// 终止条件：分段达到固定份数
	for (int i=startIndex; i<s.length(); i++) {
		// 处理节点
		分段份数变量+1
		backtracking(s, i+1, 分段份数变量);
		分段份数变量-1
		// 回溯
	}
}
## 5. 子集问题
#### 5.1 不包含重复元素——区别在于是否需要去重
模板如下(详见78)：
void backtracking(操作数组，int startIndex) {
	ending.push_back(temp); // 对于子集问题，全部push_back，相当于无终止条件。除非对个数有要求(491)
	if (startIndex >= nums.size()) // 还是加一个return 
		return ;
	for (int i=startIndex; i<s.length(); i++) {
		// 处理节点
		backtracking(操作数组, i+1);
		// 回溯
	}
}
#### 5.2 包含重复元素
5.2.1 可排序，使用used数组去重
sort(nums.begin(), nums.end());
模板如下(详见90)：
void backtracking(操作数组nums，int startIndex, vector<bool> &used) {
	ending.push_back(temp); // 对于子集问题，全部push_back，相当于无终止条件。除非对个数有要求
	if (startIndex >= nums.size()) // 还是加一个return 
		return ;
	for (int i=startIndex; i<s.length(); i++) {
		// // used[i - 1] == true，说明同一树枝candidates[i - 1]使用过,used[i - 1] == false，说明同一树层candidates[i - 1]使用过
		if (i>0 && nums[i-1] == nums[i] && used[i-1]==false)
			continue;
		// 处理节点
		used[i] = true;
		backtracking(操作数组, i+1);
		used[i] = false;
		// 回溯
	}
}
5.2.2不可排序，不用used数组，用unordered_set
模板如下(详见491)：
void backtracking(操作数组nums，int startIndex) {
	ending.push_back(temp); // 对于子集问题，全部push_back，相当于无终止条件。除非对个数有要求
	if (startIndex >= nums.size()) // 还是加一个return 
		return ;
	unordered_set<int> s; // 每一层定义新的unordered_set
	for (int i=startIndex; i<nums.length(); i++) {
		if (题目特殊要求|| s.find()!=s.end())
			continue;
		// 处理节点
		s.insert(); // 不需要再删除，代表本层元素使用过，不能再用
		backtracking(操作数组, i+1);
		used[i] = false;
		// 回溯
	}
}
## 6.排列问题——排列问题不使用startIndex,用used数组标记
#### 6.1 没有重复数字(详见46)
void backtracking(操作数组nums, vector<bool>& used) {
	返回条件：满足个数
	for (int i=0; i<nums.size(); i++) { // 排列从0开始
		if (used[i] == true) // used数组控制一个排列中一个元素使用一次，代替startIndex
			continue;
		处理节点，used[i] = true;
		backtracking(nums, used);
		回溯，used[i] = false;
	}
}
#### 6.2 包含重复数字——去重(详见47)
去重数组same+排列中一个元素使用一个数组used
sort(nums.begin(), nums.end()) // 有去重数组一定要排序
void backtracking(操作数组nums, vector<bool>& used, vector<bool>& used) {
	返回条件：满足个数
	for (int i=0; i<nums.size(); i++) { // 排列从0开始
		if (i>0 && nums[i-1]==nums[i] == same[i-1]==false)
			continue;
		if (used[i] == true) // used数组控制一个排列中一个元素使用一次，代替startIndex
			continue;
		处理节点，used[i] = true, same[i] = true;
		backtracking(nums, used, same);
		回溯，used[i] = false; same[i] = false;
	}
}
## 7. 棋盘问题
#### 7.1 一维棋盘(详见51)
void backtracking(int n, 棋盘数组tag, int row) {
	// 返回条件，一般是横坐标个数
	for (int i=0; i<n; i++) {
		操作tag
		backtracking(n, tag, row+1);
		//回溯
	}
}
#### 7.2 二维棋盘(详见37)
参数中不使用row，用一些剪枝操作让ij都从0开始
void backtracking(int n, 棋盘数组tag) {
	// 返回条件可以没有
	for (int i=0; i<n; i++) {
		for (int j=0; j<n; j++) {
			if (满足特殊条件) {
				操作tag
				backtracking(n, tag);
				//回溯
			}
		}
	}
}
