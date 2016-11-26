# stu_infromatoin
qt+database

# #学生信息管理系统 说明文档

*注：若要运行此程序需要有qt5.7环境并将`database.cpp`中的数据库路径改为本地路径*

##简介
本程序基于**SQLite**为支撑，**Qt**为界面完成，除了基本的对学生信息的增删改查功能外，还可个性化定制学生兴趣从而发现某学生的可能朋友。
##基于软件工程思想优化说明
###密文回显

*主界面*
为完善用户体验，本程序更改为两个不同的接口：教师+学生,同时添加了密文回显

![主界面.png](http://upload-images.jianshu.io/upload_images/3766646-8dc3818c457ad163.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

改进代码是文件`qpasswordlineedit.h`和相应`.cpp`文件

###修改密码
*学生界面* 


![学生界面.png](http://upload-images.jianshu.io/upload_images/3766646-c9a0329d75aec9ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

改进后，学生界面添加了**修改密码**的功能，相关代码如下：

```C++

void password::on_pushButton_save_clicked()
{
 QSqlDatabase db = QSqlDatabase::database("sqlite1"); //建立数据库连接
 QSqlQuery query(db);

 QString key=ui->lineEdit_old->text();
 query.prepare(QObject::tr("select 密码 from stu_password where 用户名 = ?"));
 query.addBindValue(ui->lineEdit_num->text());
 query.exec();
 if(!query.next())
 {
	 QMessageBox::information(NULL,"information","用户名不存在",QMessageBox::tr("OK"));
	 ui->lineEdit_num->clear();
 }
 else
 {
	 if(key==query.value(0).toString())
	 {
		 query.prepare(QObject::tr("update stu_password set 密码=? where 用户名 = ?"));
		 query.bindValue(0,ui->lineEdit_new->text());
		 query.bindValue(1,ui->lineEdit_num->text());
		
		 if(query.exec())
		 {
			 QMessageBox::information(NULL,"information","修改密码成功！",QMessageBox::tr("OK"));
			 this->close();
		 }
		 else
		 	QMessageBox::information(NULL,"information","啊哦，修改失败",QMessageBox::tr("OK"));
		
	 }
	 else
	 {
		 QMessageBox::information(NULL,"information","原密码错误",QMessageBox::tr("OK"));
		 ui->lineEdit_old->clear();
	 }
}

```



修改密码界面：
######学生端更改密码界面
![更改密码.png](http://upload-images.jianshu.io/upload_images/3766646-325cd51d1782f221.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
######老师端更改密码界面
![更改密码2.png](http://upload-images.jianshu.io/upload_images/3766646-185bb75fb28fb468.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
相应实现文件在`password.cpp` 和`password_t.cpp`以及对应`.h`文件中，分别对应学生和管理员界面的*更改密码...*

###容错
优化了容错功能

*添加学生容错*

![容错1.png](http://upload-images.jianshu.io/upload_images/3766646-d8400ee95927c10e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相关代码如下：

```
 if(query.exec())
 {
	 QMessageBox::information(NULL,"information","修改成功！",QMessageBox::tr("OK"));
	 this->close();
 }
 else
 {
	 QMessageBox::critical(NULL,"critical","所填信息不完整或有误！",QMessageBox::tr("OK"));
 }
```
*编辑学生信息容错*
![容错2.png](http://upload-images.jianshu.io/upload_images/3766646-68588ea3ae341d37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相关代码如下：

```
if(ui->label_name->text()==NULL)
 QMessageBox::information(NULL,"information","请先选择一个学生",QMessageBox::tr("OK"));
```
*修改密码容错*

![容错3.png](http://upload-images.jianshu.io/upload_images/3766646-a37cd6331d959c0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相关代码如下：

```
if(!query.next())
 {
	 QMessageBox::information(NULL,"information","用户名不存在",QMessageBox::tr("OK"));
	 ui->lineEdit_num->clear();
 }
```
```
 if(key==query.value(0).toString())
 {
	 query.prepare(QObject::tr("update stu_password set 密码=? where 用户名 = ?"));
	 query.bindValue(0,ui->lineEdit_new->text());
	 query.bindValue(1,ui->lineEdit_num->text());
	
	 if(query.exec())
	 {
		 QMessageBox::information(NULL,"information","修改密码成功！",QMessageBox::tr("OK"));
		 this->close();
	 }
	 else
	 	QMessageBox::information(NULL,"information","啊哦，修改失败",QMessageBox::tr("OK"));

 }
 else
 {
	 QMessageBox::information(NULL,"information","原密码错误",QMessageBox::tr("OK"));
	 ui->lineEdit_old->clear();
 }
```
*登录容错*

![容错4.png](http://upload-images.jianshu.io/upload_images/3766646-d27bddc98f3e718b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相关代码如下：

```
if((!query.next()))
   QMessageBox::critical(NULL,"critical","用户名不存在",QMessageBox::tr("朕知错了"));

 else
 {
   QString key1 =query.value(0).toString();
     if(key1==key)
     {
     //此处登录成功后界面
     t_view.show();
     this->show();
     }
    else
    {
       QMessageBox::critical(NULL,"critical","密码错误!",QMessageBox::tr("朕知错了")); 
       ui->name_lineEdit->clear();
       this->ui->name_lineEdit->setFocus();
    }
 }
```

