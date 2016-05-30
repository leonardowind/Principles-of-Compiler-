# Principles-of-Compiler-

#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <algorithm>
using namespace std;

#pragma region 测试数据
/*

S U V M#
a b c#
S-aS S-W S-U U-a V-bV V-ac W-aW #
S#


S W U V Y#
a b c d#
S-aS S-W S-U U-a V-bV V-ac W-aW Y-cd W-a W-b #
S#

*/
#pragma endregion 

#pragma region 全局变量 

char word;
char sub;
string str;
vector<char> Vn;
vector<char> Vt;
multimap<char, string> P;
vector<char> S;

vector<char> Vn_1;
multimap<char, string> P_1;

vector<char> Vn_2;
vector<char> Vt_2;
multimap<char, string> P_2;

#pragma endregion

#pragma region 函数声明

void output(vector<char> V);

char is_Vn(string w);

bool repeat_Vn_1(char w);

bool repeat_Vn_2(char w);

bool repeat_Vt_2(char w);

bool is_Vt(string w);

bool is_Vn_1(string w);

bool final_is_Vn_1(string w);

bool first_is_Vt(char w);

bool first_is_Vn_1(char w);

bool final_first_is_Vn_1(char w);

bool is_Vn_2(char w);

bool first_is_V_2(char w);

bool second_is_V_2(string w);

void input();

void algorithm_2_1();

void algorithm_2_2();

void output_2_1();

void output_2_2();

void output_V();

void output_P();

#pragma endregion

int main()
{
	input();
	algorithm_2_1();
	cout << "\n\n" << endl;
	cout << "算法2.1执行后：\n" << endl;
	output_2_1();
	algorithm_2_2();
	cout << "\n\n" << endl;
	cout << "算法2.2执行后：\n" << endl;
	output_2_2();
	return 0;
}

#pragma region 调用的函数

void input()
{
	cout << "输入样例格式:\n" << "S U V M#\na b c#\nS - aS S - W S - U U - a V - bV V - ac W - aW #(注意：#前有空格)\nS#" << endl << endl << "请输入Vn、Vt、P、S:" << endl;

	while (cin >> word && word != '#')//非终结符集合
		Vn.push_back(word);

	while (cin >> word && word != '#')//终结符集合
		Vt.push_back(word);

	while (1)//产生式
	{
		cin >> word;
		if (word == '#')
			break;
		cin >> sub;
		cin >> str;
		P.insert(pair<char, string>(word, str));
	}
	/*for (auto &w : P)
	cout << w.first << " -> " << w.second << endl;*/

	while (cin >> word && word != '#')//起始
		S.push_back(word);

	Vn_2 = S;
}

void output(vector<char> V)
{
	for (auto x : V)
		cout << x << ' ';
	cout << endl;
}

void output_V(vector<char> V)
{
	for (auto x : V)
		cout << x << ' ';
	cout << endl << endl;
}

void output_P(multimap<char, string> P)
{
	for (auto &w : P)
		cout << w.first << " -> " << w.second << endl;
	cout << endl;
}

void algorithm_2_1()
{
	for (auto &w : P) //对于P中每个产生式,若右部均是终结符号串, 则将A加入Vn_1中
	{
		if (is_Vt(w.second))//是否为终结符号串
			Vn_1.push_back(w.first);
	}
	//output(Vn_1);

	while (1)//重复,直到Vn_1不再增大
	{
		int size = Vn_1.size();
		for (auto &w : P)
		{
			if (is_Vt(w.second) || is_Vn_1(w.second))
			{
				if (repeat_Vn_1(w.first) == false)//没重复
					Vn_1.push_back(w.first);
			}
		}

		if (Vn_1.size() == size)
			break;
	}

	for (auto &w : P) //对于P中每个产生式,左右都是Vn_1或Vt, 则将A加入Vn_1中
	{
		if (final_first_is_Vn_1(w.first) && final_is_Vn_1(w.second))
		{
			P_1.insert(pair<char, string>(w.first, w.second));
		}
	}
	/*for (auto &w : P_1)
		cout << w.first << " -> " << w.second << endl;*/
}

void output_2_1()
{
	cout << "非终结符号集Vn_1：" << endl;
	output_V(Vn_1);
	cout << "终结符号集Vt_1：" << endl;
	output_V(Vt);
	cout << "产生式集合P_1：" << endl;
	output_P(P_1);
}

void algorithm_2_2()
{
	while (1)//重复,直到Vn_2和Vt_2不再增大
	{
		int size_Vn = Vn_2.size();
		int size_Vt = Vt_2.size();

		for (auto &w : P_1)
		{
			if (is_Vn_2(w.first))
			{
				for (char s : w.second)
				{
					if (find(Vn.begin(), Vn.end(), s) == Vn.end())
					{
						if (repeat_Vt_2(s) == false)//没重复
							Vt_2.push_back(s);
					}
					else
					{
						if (repeat_Vn_2(s) == false)//没重复
							Vn_2.push_back(s);
					}

				}
			}
		}

		if (Vn_2.size() == size_Vn && Vt_2.size() == size_Vt)
			break;
	}
	//output(Vn_2);
	//output(Vt_2);

	for (auto &w : P) //对于P中每个产生式,左右都是Vn_1或Vt, 则将A加入Vn_1中
	{
		if (first_is_V_2(w.first) && second_is_V_2(w.second))
		{
			//cout << w.first << " -> " << w.second << endl;
			P_2.insert(pair<char, string>(w.first, w.second));
		}
	}
}

void output_2_2()
{
	cout << "非终结符号集Vn_2：" << endl;
	output_V(Vn_2);
	cout << "终结符号集Vt_2：" << endl;
	output_V(Vt_2);
	cout << "产生式集合P_2：" << endl;
	output_P(P_2);
}

char is_Vn(string w)
{
	char r;
	for (char s : w)
	{
		if (find(Vn.begin(), Vn.end(), s) == Vn.end())
		{
			return 0;
		}
		else
			r = s;
	}
	return r;
}

bool repeat_Vn_1(char w)//判断Vn_1是否有重复
{
	if (find(Vn_1.begin(), Vn_1.end(), w) == Vn_1.end())
		return false;
	else
		return true;
}

bool repeat_Vn_2(char w)//判断Vn_1是否有重复
{
	if (find(Vn_2.begin(), Vn_2.end(), w) == Vn_2.end())
		return false;
	else
		return true;
}

bool repeat_Vt_2(char w)//判断Vn_1是否有重复
{
	if (find(Vt_2.begin(), Vt_2.end(), w) == Vt_2.end())
		return false;
	else
		return true;
}

bool is_Vt(string w)//是否为终结符号串
{
	for (char s : w)
	{
		if (find(Vt.begin(), Vt.end(), s) == Vt.end())
		{
			//cout << "Can't find " << w << " in Vt" << endl;
			return false;
		}

	}
	return true;
}

bool is_Vn_1(string w)
{
	for (char s : w)
	{
		if (find(Vn_1.begin(), Vn_1.end(), s) == Vn_1.end())
		{
			//cout << "Can't find " << w << " in Vt" << endl;
			return false;
		}

	}
	return true;
}

bool final_is_Vn_1(string w)
{
	for (char s : w)
	{
		if (find(Vn_1.begin(), Vn_1.end(), s) == Vn_1.end() && find(Vt.begin(), Vt.end(), s) == Vt.end())
		{
			//cout << "Can't find " << w << " in Vt" << endl;
			return false;
		}

	}
	return true;
}

bool first_is_Vt(char w)
{

	if (find(Vt.begin(), Vt.end(), w) == Vt.end())
	{
		//cout << "Can't find " << w << " in Vt" << endl;
		return false;
	}

	return true;
}

bool first_is_Vn_1(char w)
{

	if (find(Vn_1.begin(), Vn_1.end(), w) == Vn_1.end())
	{
		//cout << "Can't find " << w << " in Vt" << endl;
		return false;
	}


	return true;
}

bool final_first_is_Vn_1(char w)
{

	if (find(Vn_1.begin(), Vn_1.end(), w) == Vn_1.end() && find(Vt.begin(), Vt.end(), w) == Vt.end())
	{
		//cout << "Can't find " << w << " in Vt" << endl;
		return false;
	}


	return true;
}

bool is_Vn_2(char w)
{
	if (find(Vn_2.begin(), Vn_2.end(), w) == Vn_2.end())
	{
		return false;
	}
	return true;
}

bool first_is_V_2(char w)
{

	if (find(Vn_2.begin(), Vn_2.end(), w) == Vn_2.end() && find(Vt_2.begin(), Vt_2.end(), w) == Vt_2.end())
	{
		return false;
	}
	return true;
}

bool second_is_V_2(string w)
{
	for (char s : w)
	{
		if (find(Vn_2.begin(), Vn_2.end(), s) == Vn_2.end() && find(Vt_2.begin(), Vt_2.end(), s) == Vt_2.end())
		{
			return false;
		}

	}
	return true;
}

#pragma endregion
