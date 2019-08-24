### 0、输入处理

```c++
void cpp_input(){
	int in_int;
	string s_input;// = "2,3,4,5";
	cin >> s_input;
	replace(s_input.begin(), s_input.end(), ',', ' ');//#include <algorithm>

	stringstream sstream;//#include<stringstream>
	sstream << s_input;
	while (sstream >> in_int){
		cout << in_int << " ";
	}
	cout << endl;
	sstream.clear();//注意清空	
}
```



### 1、建立myVector

```c++
template <typename T>
class myVector
{
public:
	myVector();
	myVector(size_t  length);
	myVector(size_t  length, T t);
	~myVector();
	myVector(myVector<T> &vec);
	myVector& operator=(const myVector<T>& vec);

	T& operator[](size_t i){
		return this->m_data[i];
	}

	int size(){
		return this->len_size;
	}

	size_t capacity(){
		return this->cap_size;
	}

	bool empty(){
		return this->len_size == 0;
	}

	void push_back(T t);
	T pop_back();

private:
	T *m_data;
	size_t len_size;
	size_t cap_size;
	bool resize(size_t newcapacity);
};

template <typename T> myVector<T>::myVector():len_size(0),cap_size(0),m_data(NULL){

}

template <typename T> myVector<T>::myVector(size_t  length){
	len_size = length;
	cap_size = len_size;
	m_data = new T[cap_size];
	for (int i = 0; i < len_size; ++i){
		m_data[i] = 0;
	}
}

template <typename T> myVector<T>::myVector(size_t  length, T t){
	len_size = length;
	cap_size = len_size;
	m_data = new T[cap_size];
	for (int i = 0; i < len_size; ++i){
		m_data[i] = t;
	}
}

template <typename T> myVector<T>::myVector(myVector<T> &vec){
	len_size = vec.size();
	cap_size = vec.capacity();
	m_data = new T[cap_size];
	for (int i = 0; i < len_size; ++i){
		m_data[i] = vec[i];
	}
}

template <typename T> myVector<T>& myVector<T>::operator=(const myVector<T>& vec){
	delete[] m_data;
	m_data = NULL;
	len_size = vec.size();
	cap_size = vec.capacity();
	m_data = new T[cap_size];
	for (size_t i = 0; i < len_size; ++i){
		m_data[i] = vec[i];
	}
	return this;
}

template <typename T> myVector<T>::~myVector(){
	delete[] m_data;
	m_data = NULL;
	len_size = 0;
	cap_size = 0;
}

template <typename T> bool myVector<T>::resize(size_t newcapacity){
	if (newcapacity < len_size){
		return false;
	}
	T *new_m_data = new T[newcapacity];
	for (int i = 0; i < len_size; ++i){
		new_m_data[i] = m_data[i];
	}
	delete[] m_data;
	m_data = new_m_data;
	cap_size = newcapacity;
	return true;
}

template <typename T> void myVector<T>::push_back(T t){
	if (len_size + 1 < cap_size){
		m_data[len_size++] = t;
	}
	else{
		if (len_size == 0)	cap_size = 1;
		size_t new_cap = 2 * cap_size;
		resize(new_cap);
		m_data[len_size++] = t;
	}
	
}

template <typename T> T myVector<T>::pop_back(){
	return m_data[--len_size];
}
```



### 2、排序

```c++
vector<int> random(int n, int randmin, int randmax) { //n为随机数大小
	vector<int> randpool; //储存所有可能的随机数
	vector<int> randnum; //结果序列
	int choice;
	srand(time(NULL));
	for (int i = randmin; i < randmax; i++)	randpool.push_back(i); //生成所有可能随机数
	for (int i = 0; i < n; i++) {
		choice = rand() % randpool.size(); //随机产生下标
		randnum.push_back(randpool[choice]);
		swap(randpool[choice], randpool[randpool.size() - 1]); //交换已经生成的随机数和数组最后一个数
		randpool.pop_back(); //删除已经生成的随机数
	}
	return randnum;
}
```



```c++
class mySort{
public:
	//mySort(){
//}
    //============1冒泡排序=============
    void BubbleSort(vector<int> &vec, int len){
        cout << "BubbleSort" << endl;
        //int count = 0;
        int ordered_index = len - 1;
        int last_swap = len - 1;
        for (int i = 0; i < len - 1; ++i){
            bool is_ordered = true;
            ordered_index = last_swap; //内层循环优化：后面的元素为有序元素，不需要进行比较
            //cout << "	" << i << "	" << count << endl;
            for (int j = 0; j < ordered_index; ++j){
                //++count;
                if (vec[j] > vec[j + 1]){
                    swap(vec[j], vec[j + 1]);
                    is_ordered = false;//外层循环优化：如果有交换，说明数组仍不是有序的，继续进行
                    last_swap = j;
                }
            }
            if (is_ordered == true)	break;//外层循环优化：如果没有交换，说明数组是有序的，跳出循环
        }
    }

    //============2快速排序=============
    void QuickSort(vector<int> &vec, int len){
        cout << "QuickSort" << endl;
        Qsort(vec, 0, len - 1);
    }
    void Qsort(vector<int> &vec, int low, int high){
        //cout << "Qsort			" << low << "  low:high  " << high << endl;
        int privot;
        if (low < high){
            //随机快速排序
            int randomC = (int)((rand() % 100 / (double)100) * (high - low + 1));
            //cout << randomC << " " << low << " " << high << endl;
            swap(vec[low + randomC], vec[low]);
            //随机快速排序

            privot = Partition(vec, low, high);
            //cout << "Qsort:" << privot << "	" << low << "  low:high  " << high << endl;
            Qsort(vec, low, privot - 1);
            Qsort(vec, privot + 1, high);
        }
    }
    int Partition(vector<int> &vec, int low, int high){
        //cout << "Partition	" << low << "  low:high  " << high << endl;
        int pivotKey;
        pivotKey = vec[low];
        while (low < high){
            while (low < high && vec[high] > pivotKey)	--high;
            if (low < high)	swap(vec[low], vec[high]);
            while (low < high && vec[low] < pivotKey)	++low;
            if (low < high) swap(vec[low], vec[high]);
        }
        return low;
    }

    //============3插入排序=============
    void InserSort(vector<int> &vec, int len){
        cout << "InserSort" << endl;
        for (int i = 1; i < len; ++i){
            for (int j = i; j > 0 && vec[j - 1] <= vec[j]; --j){
                //if (vec[j - 1] <= vec[j])	break;
                //else swap(vec[j - 1], vec[j]);
                swap(vec[j - 1], vec[j]);
            }
        }
    }

    //============4shell 排序=============
    //希尔排序比插入排序快很多，它是基于什么原因呢？当gap值大的时候，数据项每一趟排序需要移动元素的个数很少，但数据项移动的距离很长。
    //当gap减小时，每一趟排序需要移动的元素的个数增多，但是此时数据项已经接近于它们排序后最终的位置，这对于插入排序可以更有效率。
    void ShellSort(vector<int> &vec, int len){
        int i, j;
        int gap = len;
        while (gap > 2){
            gap = gap / 3 + 1;
            for (i = gap; i < len; i++){//gap=1时，是直接插入排序
                //cout << gap << "	" << i << endl;
                for (j = i - gap; j >= 0 && vec[j] > vec[j + gap]; j -= gap){
                    swap(vec[j], vec[j + gap]);
                }
            }
        }
    }

    //============5选择排序=============
    void SelectSort(vector<int> &vec, int len){
        cout << "SelectSort" << endl;
        int min_val = INT_MAX;
        int min_index = 0;
        for (int i = 0; i < len - 1; ++i){
            min_val = INT_MAX;
            for (int j = i; j < len; ++j){
                if (vec[j] < min_val){
                    min_val = vec[j];
                    min_index = j;
                }
            }
            swap(vec[i], vec[min_index]);
        }
    }

    //============6堆排序=============
    void HeapSort(vector<int> &vec, int len){
        cout << "HeapSort" << endl;
        //创建一个大顶堆
        for (int s = len / 2 - 1; s >= 0; s--){
            HeapAdjust(vec, s, len - 1);
            //for (int v : vec){
            //	cout << v << "-";
            //}
            //cout << endl;
        }
        //排序
        for (int i = len - 1; i >= 1; i--){
            swap(vec[0], vec[i]);
            HeapAdjust(vec, 0, i - 1);
            //for (int v : vec){
            //	cout << v << "-";
            //}
            //cout << endl;
        }
    }

    void HeapAdjust(vector<int> &vec, int s, int m){
        //s是当前节点，j=2s+1是左子节点，j+1是右子节点
        for (int j = 2 * s + 1; j <= m; j = 2 * j + 1){
            if (j + 1 <= m && vec[j] < vec[j + 1]){//选择子节点中大的一个
                j++;
            }
            if (vec[s]>vec[j]){//判断 当前节点 是否 大于 最大的子节点
                break;
            }
            swap(vec[s], vec[j]);//否，交换父子节点，并下一轮判断
            s = j;
        }
    }

    //============7归并排序=============
    void MergeSort(vector<int> &vec, int len){
        cout << "MergeSort" << endl;
        MSort(vec, 0, len - 1);
    }
    void MSort(vector<int> &vec, int low, int high){
        if (low < high){
            int mid = (low + high) / 2;
            MSort(vec, low, mid);
            MSort(vec, mid + 1, high);
            Merge(vec, low, mid, high);
        }
    }
    void Merge(vector<int> &vec, int low, int mid, int high){
        int i, j, k = 0;
        vector<int> sortvec(vec.begin() + low, vec.begin() + high + 1);
        for (i = low, j = mid + 1; i <= mid && j <= high;){
            if (vec[i] <= vec[j]){
                sortvec[k++] = vec[i++];
            }
            else{
                sortvec[k++] = vec[j++];
            }
        }
        while (i <= mid){
            sortvec[k++] = vec[i++];
        }
        while (j <= high){
            sortvec[k++] = vec[j++];
        }
        for (int l = 0; l < k;){
            vec[low++] = sortvec[l++];
        }
    }

    //============8计数排序=============
    void CountSort(vector<int> &vec, int len){
        cout << "CountSort" << endl;
        int max_num = vec[0];
        int min_num = vec[0];
        for (int v : vec){
            max_num = v > max_num ? v : max_num;
            min_num = v < min_num ? v : min_num;
        }
        vector<int> countvec(max_num - min_num + 1, 0);
        for (int v : vec){
            countvec[v - min_num]++;
        }
        int k = 0;
        for (int i = 0; i < countvec.size(); ++i){
            //cout << " k:" << k << " i:" << i << " countvec[i]:" << countvec[i] << endl;
            while (countvec[i]--){
                vec[k++] = min_num + i;
            }
        }
    }

    //============9桶排序=============
    void BucketSort(vector<int> &vec, int len, int bucketsize){
        cout << "BucketSort" << endl;
        int max_num = vec[0];
        int min_num = vec[0];
        for (int v : vec){
            max_num = v > max_num ? v : max_num;
            min_num = v < min_num ? v : min_num;
        }
        if (len < 2 || min_num == max_num)	return;
        int bucketcount = (int)((max_num - min_num) / bucketsize + 1);//bucketsize一个桶的范围，如0-10
        vector<vector<int> > countvec(bucketcount);
        int bucketindex;
        for (int v : vec){//装桶
            bucketindex = getBucketCount(v, bucketsize, min_num, max_num);
            countvec[bucketindex].push_back(v);
        }
        for (int i = 0; i < countvec.size(); ++i){//桶内排序
            //QuickSort(countvec[bucketindex], countvec[bucketindex].size());
            //SelectSort(countvec[bucketindex], countvec[bucketindex].size());
            //QuickSort(countvec[i], countvec[i].size());//是i，不是bucketindex；
            SelectSort(countvec[i], countvec[i].size());//是i，不是bucketindex；
        }
        int k = 0;
        for (int i = 0; i < countvec.size(); ++i){
            for (int j = 0; j < countvec[i].size(); ++j){
                vec[k++] = countvec[i][j];
            }
        }
    }
    inline int getBucketCount(int num, int size, int min, int max){
        int count = (int)((num - min) / size);
        return count;
    }

    //============9基数排序=============
    void RadixSort(vector<int> &vec, int len){
        vector<vector<int> > radixvec(10);
        int radix_index;
        for (int v : vec){//装桶
            radix_index = v % 10;
            radixvec[radix_index].push_back(v);
        }
        int k = 0;
        for (int i = 0; i < radixvec.size(); ++i){//出桶
            for (int j = 0; j < radixvec[i].size(); ++j){
                vec[k++] = radixvec[i][j];
            }
        }
        radixvec.clear();//!!!!清空桶
        radixvec.resize(10);//!!!!重新分配空间
        for (int v : vec){//装桶
            radix_index = v / 10;
            radixvec[radix_index].push_back(v);
        }
        k = 0;
        for (int i = 0; i < radixvec.size(); ++i){//出桶
            for (int j = 0; j < radixvec[i].size(); ++j){
                vec[k++] = radixvec[i][j];
            }
        }
    }
};
```



### 3、top k 

```c++
class Solution2 {//冒泡排序 排 top K;
public:
	bool comp(int a, int b){
		return a>b;
	}
	vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
		vector<int> res;
		int n = input.size();
		if (n == 0 || k == 0)    return res;
		if (n <= k)    return input;
		for (int i = 0; i < k; ++i){
			for (int j = 1; j < n; ++j){
				if (input[j] > input[j - 1])	swap(input[j], input[j - 1]);
			}
		}
		for (int i = 0; i < k; ++i){
			res.push_back(input[n - 1 - i]);
			cout << input[n - 1 - i] << "->";
		}
		cout << endl;
		return res;
	}
};
```



```c++
class Solution3 {//multiset 排序
public:
	bool comp(int a, int b){
		return a>b;
	}
	vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
		vector<int> res;
		multiset<int> topk;
		int n = input.size();
		if (n == 0 || k == 0)    return res;
		if (n <= k)    return input;
		for (int i = 0; i < k; ++i){
			topk.insert(input[i]);
		}
		for (int i = k; i < n; ++i){
			//cout << input[i] << "	" << *(--topk.end()) << endl;
			if (input[i] < *(--topk.end())){
				topk.erase(--topk.end());
				topk.insert(input[i]);
			}
		}
		for (int i:topk){
			res.push_back(i);
			cout << i << "->";
		}
		cout << endl;
		return res;
	}
};
```



```c++
class Solution4 {
public:
	vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        int n = input.size();
        if (n == 0 || k == 0)    return res;
        if (n <= k)    return input;
        for (int i = 0; i < k; ++i){
            res.push_back(input[i]);
        }
        std::make_heap(std::begin(res), std::end(res));//默认是大顶堆	//,std::greater<>()小顶堆
        //for (int i : res){
        //	cout << i << "->";
        //}
        //cout << "\n*****"<<endl;
        for (int i = k; i < n; ++i){
            if (input[i] < *(res.begin())){
                std::pop_heap(std::begin(res), std::end(res));
                res.pop_back();
                res.push_back(input[i]);
                std::push_heap(std::begin(res), std::end(res));
            }
        }
        //auto iter = std::is_heap_until(std::begin(res), std::end(res));//判断是否是 堆
        //if (iter != std::end(res)){
        //	std::cout << "res is a heap up to " << *iter << std::endl;
        //}else{
        //	std::cout << "res is a heap "<< std::endl;
        //}
        return res;
    }
};
```



```c++
class Solution5 {
public:
    vector<int> GetLeastNumbers_Solution(vector<int> input, int k) {
        vector<int> res;
        int begin_index = 0;
        int end_index = input.size() - 1;
        quick_sort(input, begin_index, end_index, k);
        for (int i = 0; i < k; i++){
            res.push_back(input[i]);
        }
        return res;
    }

    int helper(vector<int> &input, int i, int j){
        int comp = input[i];
        int index_left = i;
        int index_right = i;
        int index_k;
        for (int k = i; k <= j; ++k){
            if (input[k] < comp){
                index_k = k;
                for (int d = index_right; d >= index_left; --d){
                    //cout << k <<index_k << d << endl;
                    swap(input[index_k], input[d]);
                    index_k = d;
                }
                ++index_right;
                ++index_left;
            }
            else{
                ++index_right;
            }
        }
        return index_left;
    }

    int helper2(vector<int> &input, int i, int j){
        int comp = input[i];
        int index_left = i;
        int index_right = j;
        int index_k = i;
        while (index_left < index_right){
            while (index_left < index_right){
                if (input[index_right] < comp){
                    break;
                }
                else{
                    --index_right;
                }
            }
            while (index_left < index_right){
                if (input[index_left] > comp){
                    break;
                }
                else{
                    swap(input[index_left], input[index_k]);
                    index_k = index_left;
                    ++index_left;
                }
            }
            if (index_left >= index_right)	break;
            swap(input[index_left], input[index_right]);
            swap(input[index_left], input[index_k]);
            index_k = index_left;
            ++index_left;
            --index_right;
        }
        //cout << input[index_k] << input[index_right] << endl;
        if (input[index_k] > input[index_right] && index_left == index_right){
            swap(input[index_k], input[index_right]);
            index_k = index_right;
        }
        return index_k;
    }

    void quick_sort(vector<int> &vec, int low, int high, const int k){	
        if (low == high) return;
        //int i = partition(vec, low, high);
        int i = helper2(vec, low, high);
        cout << "low~high: " << low << "~" << high << "	 sort lenth:" << high - low << "	posi:" << i << endl;
        for (int k = 0; k<vec.size(); k++){
            cout << vec[k] << "-";
        }
        cout << endl << endl;
        if (i == k - 1 || i == k)	return;
        if (i > k-1){
            high = --i;
            quick_sort(vec, low, high, k);
        }	
        if (i < k-1){
            low = ++i;
            quick_sort(vec, low, high, k);
        }	

    }
};
```



### 4、补码1的个数

```c++
class Solution {
public:
	int  NumberOf1(int n) {
		if (n == INT_MIN)	return 1;
		int res = 0;
		int num = n;
		vector<int> mybit(32, 0);
		if (n<0){
			num = -n;
		}
		//求二进制
		int tmp1;
		for (int i = 31; i >= 0; --i){
			tmp1 = num % 2;
			if (tmp1 == 1)   mybit[i] = 1;
			num = (num - tmp1) / 2;
			if (num == 0)    break;
		}
		if (n<0){
			mybit[0] = 1;//符号位不变
			//数值部分取反，求反码
			for (int i = 1; i<32; ++i){
				cout << mybit[i];
				mybit[i] = (mybit[i] == 1 ? 0 : 1);
			}
			cout << endl;
			//求补码，反码加1
			tmp1 = 1;
			for (int i = 31; i >= 0; --i){
				tmp1 = (mybit[i] + tmp1);
				mybit[i] = tmp1 % 2;
				tmp1 = tmp1 / 2;
			}
		}
		//统计1的个数
		for (int i = 0; i<32; ++i){
			if (mybit[i] == 1) res++;
		}
		return res;
	}

	int  NumberOf1_1(int n) {
		int count = 0;
		while (n != 0) {
			++count;
			n = (n - 1) & n;
		}
		return count;
	}

	int  NumberOf1_2(int n) {
		int count = 0;
		unsigned int flag = 1;
		while (flag){
			if (n & flag){
				count++;
			}
			flag = flag << 1;
		}
		return count;
	}
};
```



### 5、字典序

```c++
int dictionaryNum(int n, int k){
	vector<string> nums(n," ");
	int count = 0;
	int tmp;
	for (int i = 0; i < n; i++){
		nums[i] = to_string(i + 1);
		cout << nums[i] << endl;
	}
	sort(nums.begin(),nums.end());
	for (int i = 0; i < n; i++){
		count++;
		tmp = atoi(nums[i].c_str());
		cout << tmp << endl;
		if (count == k) break;
	}
	return tmp;
}
```



### 6、随机不重复数组

```c++
int rand_array(){
	for (int i = 0; i < 12; i++){
		nums[i] = i + 1;
		cout << nums[i]<<" ";
	}
	cout << endl;
	for (int i = nums.size() - 1; i >0; i--){
		int tmp = rand() % (i + 1);//从后往前，从前面的数选一个和自己交换
		swap(nums[i], nums[tmp]);
	}
	for (int num:nums){
		cout << num << " ";
	}
	cout << endl;
}
```



###  6、二叉树遍历

```
class treeView{
public:
	void preOrder2(TreeNode* root, vector<int>& vec){
		if (root == nullptr) return;
		TreeNode *pr = root;
		stack<TreeNode*> ts;
		while (pr || ts.size()){
			if (pr){
				vec.push_back(pr->val);
				ts.push(pr);
				pr = pr->left;
			}
			else{
				pr = ts.top();
				ts.pop();
				pr = pr->right;
			}
		}
		return;
	}
	void midOrder2(TreeNode* root, vector<int>& vec){
		if (root == nullptr) return;
		TreeNode *pr = root;
		stack<TreeNode*> ts;
		while (pr || ts.size()){
			if (pr){
				ts.push(pr);
				pr = pr->left;
			}
			else{
				pr = ts.top();
				ts.pop();
				vec.push_back(pr->val);
				pr = pr->right;
			}
		}
		return;
	}
	
	void aftOrder2(TreeNode* root, vector<int>& vec){
        if (root == nullptr) return;
        TreeNode *pr = root;
        TreeNode *ptmp;
        stack<TreeNode*> ts;
        while (pr || ts.size()){
            if (pr){
                ts.push(pr);
                pr = pr->left;
            }
            else{
                pr = ts.top();
                ts.pop();
                ptmp = pr;
                pr = pr->right;
                if (ptmp->right){
                    ptmp->left = NULL;
                    ptmp->right = NULL;
                    ts.push(ptmp);
                }
                else{
                    vec.push_back(ptmp->val);
                }				
            }
        }
        return;
    }

    void cengxu2(TreeNode* root, vector<int>& vec){
        if (root == nullptr) return;
        TreeNode *pr = root;
        queue<TreeNode*> qt;
        qt.push(pr);
        while (qt.size()){
            int n = qt.size();
            for (int i = 0; i < n; ++i){
                TreeNode *tmp = qt.front();
                qt.pop();
                vec.push_back(tmp->val);
                if (tmp->left)	qt.push(tmp->left);
                if (tmp->right)	qt.push(tmp->right);
            }
        }
        return;
    }
};
```

