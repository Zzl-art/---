<center>
<label style="font-size:50px">作品介绍<label>
</center>
#一、概述
 做一个预习作业：连接数据库，通过一种静态语言，实现对数据库的增加、删除、修改、查询。因为在大学内只学习过C++，所以我还是选择了C++，作为一个静态语言来连接数据库，进行增删改查询。下面我就简单来介绍一下。

#二、准备
##1.什么是数据库？
百度百科这样的解释：数据源是指数据库应用程序所使用的数据库或者数据库服务器。 连接数据库首先需要有一个数据库，这个数据库的格式选择是怎么样的，是需要我考察的一个点，经查找，有了，SQLsever，OBDC，access这三种基本形式，还有动态库，和静态库之分，简单起见，我选择了静态库，和大一期间学习过的access作为我的目标。
**连接字符串的问题？**
     这个问题非常的关键，但是我就是因为这个搞不明白所以一直导致数据库连接不成功。现在我们来看一下。

Open("Provider=Microsoft.Jet.OLEDB.4.0;DataSource=ExamOnline.mdb","","",adModeUnknown);Microsoft.Jet.OLEDB.4.0：具体是指驱动的版本号。一般4.0的是指的老版本，现在最新的是12.0。它们俩的区别应该就是在识别access格式的差距。这个在后面给我造成了很多困扰

#三、连接
1. 新建项目staticlib
2. 加入 
"#import "C:\Program Files\Common Files\System\ado\msado15.dll" no_namespace rename("EOF", "adoEOF")"放在stdafx.h的最后面。
3. 创建类sql
```C++
#include"stdafx.h"            //因为是新建了个类，所以需要包含头文件
class sql
{
public:
	_ConnectionPtr m_pConnection;                   //连接access数据库的链接对象
	_RecordsetPtr m_pRecordset;                     //结果集对象
public:
	sql();                                          //定义构造函数,来进行数据库连接的初始化
	void sql_close();                               //关闭数据库连接
};
 
sql::sql()
{
	try{
		::CoInitialize(NULL);
		m_pConnection.CreateInstance("ADODB.Connection");  //创建连接对象实例
		_bstr_t strConnect = "Provider=Microsoft.Jet.OLEDB.4.0;Data Source=demo1.mdb;";//连接字符串
		m_pConnection->Open(strConnect,"","",adModeUnknown); //打开数据库
	}
	catch (_com_error e){
		AfxMessageBox(e.Description());
	}
 
}
void sql::sql_close()                                    //关闭数据库
{
	if (m_pRecordset->GetState() == adStateOpen)
	{ 
		m_pRecordset->Close();
	}	
	m_pConnection->Close();
	m_pConnection.Release(); 
	::CoUninitialize();
 
}
```
4. 载入文件
![]( http://mrw.so/6lPDtC "vs连接数据库操作")
![](http://mrw.so/6sFTe3)
![]( http://mrw.so/6sFTkF)
#四、操作代码
```C++
 #include"stdafx.h"
class ADOConn
{
public:
	_ConnectionPtr m_pConnection;                   //连接access数据库的链接对象
	_RecordsetPtr m_pRecordset;                     //结果集对象
public:
	void OnInitADOConn();                           //初始化连接对象 
	void ExitConn();								//关闭数据库连接
	_RecordsetPtr& GetRecordSet(_bstr_t bstrSQL);	//获取记录集
	BOOL ExecuteSQL(_bstr_t bstrSQL);				//执行数据库语句
	bool addMessage();                              //添加信息
	bool updata();									//修改信息
	bool deledata();								//删除信息
	bool index();									//查找信息
};
//初始化连接对象
void ADOConn::OnInitADOConn()
{
	//初始化OLE/COM环境
	::CoInitialize(NULL);
	try
	{
		m_pConnection.CreateInstance("ADODB.Connection");  //创建连接对象实例
		_bstr_t strConnect = "Provider=Microsoft.ACE.OLEDB.12.0;Data Source=demo.accdb;";//连接字符串
		m_pConnection->Open(strConnect, "", "", adModeUnknown); //打开数据库
	}
	catch(_com_error e)
	{
		//显示错误信息
		AfxMessageBox(e.Description());
	}
 
}
//关闭连接
void ADOConn::ExitConn()
{
	if (m_pRecordset != NULL)
	{
		m_pRecordset->Close();
	}
	m_pConnection->Close();
	//释放环境
	::CoUninitialize();
}
//返回结果集
_RecordsetPtr& ADOConn::GetRecordSet(_bstr_t bstrSQL)
{
	try
	{
		if (m_pConnection == NULL) //判断Connection对象是否为空
			OnInitADOConn(); //如果为空则重新连接数据库
		m_pRecordset.CreateInstance("ADODB.Recordset"); //创建记录集对象
		//获取数据表中的数据
		m_pRecordset->Open(bstrSQL, m_pConnection.GetInterfacePtr(), adOpenDynamic, adLockOptimistic, adCmdText);
	}
	catch (_com_error e) //捕获异常
	{
		AfxMessageBox(e.Description()); //显示错误信息
	}
	return m_pRecordset;
}
//执行sql语句
BOOL ADOConn::ExecuteSQL(_bstr_t bstrSQL)
{
	try
	{
		if (m_pConnection == NULL) //判断Connection对象是否为空
			OnInitADOConn(); //如果为空则重新连接数据库
		m_pConnection->Execute(bstrSQL, NULL, adCmdText); //执行数据库语句
		return true;
	}
	catch (_com_error e) //捕获异常
	{
		AfxMessageBox(e.Description()); //显示错误信息
		return false;
	}
}
//添加信息
bool ADOConn::addMessage()
{
	
		if (m_pConnection == NULL) //判断Connection对象是否为空
			OnInitADOConn(); //如果为空则重新连接数据库
		else
		{
			_variant_t RecordsAffected;                        //定义插入对象
			CString AddSql;
			AddSql.Format(_T("INSERT INTO student(ID,name,age) VALUES('123457','小白',12)"));//执行sql语句
			try{
				m_pConnection->Execute((_bstr_t)AddSql, &RecordsAffected, adCmdText);
				AfxMessageBox(_T("添加用户成功！"));
			}
			catch (_com_error e){
				AfxMessageBox(_T("添加用户失败！"));
			}
		}
	
}
//删除信息
bool ADOConn::deledata()
{
	CString str;
	try
	{
		str.Format(_T("DELETE from student where ID='123456'"));
		_variant_t RecordsAffected;
		m_pConnection->Execute((_bstr_t)str, &RecordsAffected, adCmdText);
		AfxMessageBox(_T("删除成功!"));
		return true;
	}
	catch (_com_error*e)
	{
		AfxMessageBox(e->ErrorMessage());
	}
}
//查找信息
bool ADOConn::index()
{
	// TODO:  在此添加控件通知处理程序代码
	updata();
	CString strName;
	CString strSex;
 
	try
	{
		m_pRecordset.CreateInstance(__uuidof(Recordset));
		CString search_sql;
		search_sql.Format(_T("SELECT * FROM student"));
		m_pRecordset = GetRecordSet(search_sql.AllocSysString());//查询结果集
		//m_pRecordset = ((CADO_ACCESSDlg*)(AfxGetMainWnd()))->m_pConnection->Execute(search_sql.AllocSysString(), NULL, adCmdText);
		while (!m_pRecordset->adoEOF)
		{
			strName = m_pRecordset->GetCollect("name").bstrVal;
			strSex = m_pRecordset->GetCollect("sex").bstrVal;
			m_pRecordset->MoveNext();
		}
		m_pRecordset->Close();
	}
	catch (_com_error e)
	{
		AfxMessageBox(_T("搜索失败！"));
		return;
	}
}
//更新信息
bool ADOConn::updata()
{
	CString str;
	try
	{
		str.Format(_T("update student set name='小草' where id='123789'"));
		_variant_t RecordsAffected;
		m_pConnection->Execute((_bstr_t)str, &RecordsAffected, adCmdText);
		AfxMessageBox(_T("修改成功!"));
	}
	catch (_com_error*e)
	{
		AfxMessageBox(e->ErrorMessage());
	}
}
```
