

#C++标准模板库（STL）



## ☆迭代器（）

大多数情况下，标准的C++容器都提供了`begin()`和`end()`函数，以支持迭代器。

定义mySTL是一个容器（vector或set或者….等等）

有：

``````c++
//假如是一个set    set<int> mySet;
for(set<int>::iterator it=mySet.begin();it!=mySet.end();it++){
    cout<<*it<<endl;
}//for循环里不能用it<end,最后输出值时要写成*it

//上面太麻烦，c++11的auto用法 ↓↓↓
for(auto it=mySet.begin();it!=mySet.end();it++){
    cout<<*it<<endl;
}

//或者
for(auto& it:mySet){
    cout<<it<<endl;//这里的auto it相当于直接匹配mySet里面元素的类型，直接输出it
}
//其实可以把it写成element，为了避免混淆迭代器用法

//但有些情况不能直接遍历元素，比如在循环里要用到迭代器it的时候，比如：
		for(auto it=res.begin();it!=res.end();){
			for(auto it1=user[*it].begin();it1!=user[*it].end();){
				if(area[*it1].first>i||area[*it1].second<i){
					it1=user[*it].erase(it1);//一定要在前面加it1=，记住 
				}
				else{
					it1++;
				}
			}
			if(user[*it].empty()){
				it=res.erase(it);
			}
			else{
				it++;
			}
		}
``````



## ☆vector

#####声明&初始化 :

```c++
//声明

vector<typename> v; //typename可以是任何基本类型，int,string等，也可以是自定义类型
vector<typename> v[500]; //相当于二维，第一维的长度已经固定为500  （表示定义一个数组，数组中每个元素都是一个int类型）
vector<vector<int> > v; //typename也可以是STL容器，这也相当于二维，只不过两个维的长度都可变

//声明+初始化

vector<int> v;
v={1,2,3,4};
//或者
vector<int> v={1,2,3,4};
//还有
vector<int> v(100, 5); // 创建一个包含100个元素，每个元素的值都为5
```

#####函数&用法 ：

``````c++
v.size();
v.push_back(x);//把x添加到末尾
v.pop_back();//删除末尾元素
v.empty();
v.clear();//复杂度O(n)
v.insert(it,x) //复杂度O(n),在迭代器it处插入x, 如  v.insert(v.begin()+2, 25); 
               //v[i]和*(v.begin()+i)是等价的。注意：除了vector和string外的其他容器不支持*(it+i)的访问方式，只能通过迭                  代器访问
v.erase(it) //复杂度O(n)，删除迭代器it处的元素
v.erase(first, last) //删除 [迭代器first, 迭代器last) 内所有的元素
``````



## ☆set

不含重复元素，且内部自动按递增排好序。

##### 声明&初始化 ：

``````c++
set<typename> mySet;
set<typename> mySet[100];//每个mySet[i]都是一个set容器

//其中，typename若是自定义结构体，一定要定义比较方式，如：
struct Role{
	string name;
	set<string> operate;
	set<string> type;
	set<string> sName;
	bool operator<(const Role& other) const {
        return name < other.name; // 这里可以根据具体需求自定义比较规则
    }
};
set<role> role;
``````

#####函数&用法 ：

``````c++
mySet.size();
mySet.insert(x); //O(logN)
mySet.erase(it); //O(1),可以结合find()函数使用。 为避免出错，写成it=mySet.erase(it)
mySet.erase(first, last); //O(last-first), 左闭右开，都是迭代器
mySet.find(x); //O(logN)，返回值为x的迭代器，如 auto it=mySet.find(2);  mySet.erase(mySet.find(2));
mySet.clear(); //O(N)
``````



## ☆string

每个元素都是一个char类型

##### 声明&初始化 ：

``````c++
string str;
string str="abcd";
``````

#####函数&用法 ：

``````c++
//string加法（+，+=），将两个string拼起来
//string大小比较，"abc"<"acc", "aa"<"aaa"
str.size(); str.length(); //一样的

insert:  
str.insert(3,str1); //在str处从插入字符串str2
str.insert(str.begin()+3, str2.begin(), str2.end()); //三个参数都是迭代器，表示str2的[begin(),end())插到str[3]上

erase:
str.erase(it);
str.erase(first, last); // [first,last)

str.clear();
str.substr(pos,len); //返回从pos号位开始，长度为len的子串
string::npos  //是一个常数，主要用于和find函数搭配
str.find(str2); //当str2是str的子串时，返回其在str中第一次出现的位置。如果str2不是str的子串，那么返回string::npos
str.find(str2.pos); //从str的pos位开始匹配str2，其他同上
    // find函数复杂度O(n*m),n和m分别是str和str2的长度，不如KMP算法O(n+m)
str.replace(pos,len,str2); //把str从pos位开始、长度为len的子串替换为str2
str.replace(it1,it2,str2); //把str的迭代器[it1,it2)范围的子串替换为str2

//string类型和int类型的互相转换
int num = stoi(str);          //string 转 int
string str = to_string(num);  //int 转 string
``````



## ☆unordered_map

##### 声明&初始化 ：

``````c++
unordered_map<typename1,typename2> m;
unordered_map<string, int> m = {{"apple", 3}, {"banana", 2}, {"cherry", 5}};

//实现任意基本类型映射到任意基本类型，如果里面是键（key）是容器或自定义结构体，则必须要自定义哈希函数映射。（如果是值（value）的话，什么类型都可）
//如：

struct Node{
	int n;
	char ch;
};
unordered_map<char, Node> m;       //这样可以，但unordered_map<Node, char> m;就不可以

unordered_map<string,set<int> > m;
unordered_map<string, vector<vector<int> > > m;  //这两个也都可，但键和值对换就不可以

//如果对换，需要这样写：
//一、当键(key)是容器的时候：  这里的std::不能省，不知道为什么
struct PairHash {
    std::size_t operator()(const std::pair<int, int>& key) const {
        // 使用异或操作符 ^ 来组合两个整数的哈希值
        std::size_t hash = std::hash<int>{}(key.first) ^ std::hash<int>{}(key.second);
        return hash;
    }
};
unordered_map<pair<int,int>,int,PairHash> green;

//如果是vector<int>
// 自定义哈希函数，用于哈希 std::vector<int>
struct CustomHash {
    std::size_t operator()(const std::vector<int>& vec) const {
        std::size_t hash = 0;
        for (int value : vec) {
            hash ^= std::hash<int>{}(value); // 使用 std::hash 对每个整数进行哈希
        }
        return hash;
    }
};

// 自定义相等性比较函数，用于比较两个 std::vector<int> 是否相等
struct CustomEqual {
    bool operator()(const std::vector<int>& vec1, const std::vector<int>& vec2) const {
        return vec1 == vec2;
    }
};
unordered_map<vector<int>, string, CustomHash, CustomEqual> customHashMap;

//如果是set<int>
struct CustomHash {
    std::size_t operator()(const std::set<int>& key) const {
        std::size_t hash = 0;
        for (int element : key) {
            // 使用异或操作符 ^ 来组合元素的哈希值
            hash ^= std::hash<int>{}(element);
        }
        return hash;
    }
};
unordered_map<set<int>, string, CustomHash> customHashMap;


//二、当键(key)是自定义结构体的时候：
// 自定义结构体 MyStruct
struct MyStruct {
    int x;
    int y;
    
    // 自定义相等性比较函数，用于比较两个 MyStruct 是否相等
    bool operator==(const MyStruct& other) const {
        return x == other.x && y == other.y;
    }
};

// 自定义哈希函数，用于哈希 MyStruct
struct CustomHash {
    std::size_t operator()(const MyStruct& key) const {
        // 简单的哈希算法，可以根据实际情况修改
        return std::hash<int>{}(key.x) ^ std::hash<int>{}(key.y);
    }
};
unordered_map<MyStruct, string, CustomHash> customHashMap;


``````

##### 函数&用法

``````c++
unordered_map<string, int> myMap;

//插入元素
myMap["Alice"] = 25;
myMap["Bob"] = 30;
//或
myMap.insert({"grape", 4});

//检查键是否存在：
if (myMap.find("David") != myMap.end()) {
    cout << "David's age is: " << myMap["David"] << endl;
} else {
    cout << "David is not in the map." << endl;
}
//或者count，只会返回1（存在）或 0（不存在）
if(myMap.count("Bob")>0)......

//删除元素
myMap.erase("Bob");
//清除
myMap.clear();

//迭代
for (const auto& pair : myMap) {
    cout<<pair.first<<" " <<pair.second<<endl;
}
//或者
for(auto it=myMap.begin();it!=myMap.end();it++){
    cout<<it->first<<" "<<it->second<<endl;
}
``````



## ☆queue和stack

##### 队列 ：

``````c++
queue<typename> q;
q.push(x); // x入队
q.pop();   // 弹出队首元素
q.front(); // 返回队首元素（不会移走元素）
q.back();  // 返回队尾元素（不会移走元素）
q.empty();
q.size();
``````

##### 栈 ：

``````c++
stack<typename> s;
s.push(x); //x入栈
s.pop();   //弹出栈顶元素
s.top();   //返回栈顶元素（不会移走元素）
s.empty();
s.size();
``````



## ☆priority_queue

优先队列，堆实现，队首元素是队列中优先级最高的那个，默认最大堆

##### 函数 ：

``````c++
priority_queue<int> q;
q.push(x); //O(logN)
q.pop();   //O(logN)
q.top();  //这里和queue不一样，是top()表示队首元素，而不是front()
q.empty();
q.size();
//注意：无论是stack，queue，还是priority_queue,都没有clear(),只能循环用pop()清空
``````

##### priority_queue内元素优先级设置 ：

``````c++
//一、基本元素优先级设置：
//默认情况下，数字大的优先级更高，队首元素最大（如int double char string类型）
//所以下面两种定义是等价的↓
priority_queue<int> q;
priority_queue<int, vector<int>, less<int> >q;

//如果想让小的排队首↓
priority_queue<int, vector<int>, greater<int> >q;

//二、结构体的优先级设置
//同前面说的set一样，在结构体里面定义↓
struct Role{
	string name;
	set<string> operate;
	set<string> type;
	set<string> sName;
	bool operator<(const Role& other) const {
        return name < other.name; // 这里可以根据具体需求自定义比较规则
    }
};
//然后就跟基本元素的优先级设置一样了，比如：
priority_queue<Role, vector<Role>, less<Role> >q;

//三、如果元素是容器
// 自定义比较类
struct CustomCompare {
    bool operator()(const std::vector<int>& a, const std::vector<int>& b) const {
        return a[1] > b[1]; // 按向量中的第二个整数值进行降序排列，即第二个整数值小的排在队首
    }
};
priority_queue<vector<int>, vector<vector<int> >, CustomCompare> q;
``````



## ☆pair

``````c++
pair<typename1,typename2> p;
pair<string,int> p("haha", 5);
p=make_pair("hahaha", 55);
p.first="hahahaha";
p.second=555;
//pair的比较，先比较first的大小，first大小相等，才比较second的大小
``````



## ☆list

1. 创建一个 `std::list` 对象：

```c++
list<int> myList; // 创建一个整数类型的链表
```

1. 插入元素：

```c++
myList.push_back(10); // 在链表末尾插入元素
myList.push_front(5); // 在链表头部插入元素
```

1. 访问元素：

```c++
int firstElement = myList.front(); // 获取链表头部元素
int lastElement = myList.back(); // 获取链表末尾元素
```

1. 迭代访问元素：

```c++
list<int>::iterator it;
for (it = myList.begin(); it != myList.end(); ++it) {
    // 使用 *it 访问当前元素
}
```

1. 删除元素：

```c++
myList.pop_back(); // 删除链表末尾元素
myList.pop_front(); // 删除链表头部元素
```

1. 插入和删除指定位置的元素：

```c++
list<int>::iterator it = myList.begin();
++it; // 移动到下一个位置
myList.insert(it, 7); // 在当前位置之前插入元素
myList.erase(it); // 删除当前位置的元素
```

1. 获取链表大小：

```c++
int size = myList.size();
```

1. 清空链表：

```c++
myList.clear();
```

1. 检查链表是否为空：

```c++
bool isEmpty = myList.empty();
```
