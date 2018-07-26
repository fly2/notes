## python自定义函数

### 数据处理

#### onehot

即将一行的值转换为多列的01值

```python
def row2col(x,colname,collen):
    '''
    行列转换，即onehot转换
    参数
    ----------------------
    x 为输入的行
    colname 名字基础
    collen 列个数
    '''
   
    col=[colname+str(i) for i in range(collen)]
    result=pd.Series(np.zeros(collen),index=col)
    #result[x[dn]]=1
    result[x[colname]]=1
    return result

#example
result_weekday=feature_test.apply(lambda x:row2col(x,'weekday',7),axis=1)
result_weekday=result_weekday.astype('int8')
for i in result_weekday.columns:
    feature_test[i]=result_weekday[i]
```



#### 科大讯飞音转字yaml文件处理

```python
def get_yaml(path,file_list,time1,n=0):
    '''
    得到录音文件
	
	参数
    -----------------------------
    path 等待提取的文本文件路径
    file_list 需要获取的文件名列表
    time1 初始时间
    n 已经提取的文本数，用于录音文件放在多个文件夹时统计提取数目
    
    返回
    -------------------------
    content 对话文本
    filename yaml文件名
    textlist yaml文件文本内容
    n 已经提取的文本数
    '''
    textlist=[]
    content=[]
    filename=[]
    file_set=set(file_list)
    list_len=len(file_list)
    for root,dirs,files in os.walk(path):
        for filespath in files:
            if filespath in file_set:
                with open(os.path.join(root,filespath),'r') as file:
                    text=file.read()
                    text=text.replace('|','')
                    text1=re.sub('channel: n0\n.*?\n.*?text:','n0:',text)
                    text2=re.sub('channel: n1\n.*?\n.*?text:','n1:',text1)
                    text3=re.findall('n[01]:.*?\n',text2,flags=re.S)
                    text4=''.join(text3)
                    content.append(text4)
                    filename.append(filespath)
                    textlist.append(text)
                    n=n+1
                if n%500==0:
                    pass
                    print('%d/%d time %f m'%(n,list_len,(time.time()-time1)/60))
    return content,filename,textlist,n
```

#### 将分词后的文本转换为文档向量

##### 利用doc2vec模型

```python
import numpy as np



def doc2vec(text,model):
    '''
    将分词文本转换为词向量
    
    参数
    ----------------------
    text 分词后的文本内容
    model doc2vec的模型
    
    返回
    ----------------------
    doc2vec 文档向量
    '''
    n1=0
    doc2vec=np.zeros((len(text),256))
    for i in text:
        doc2vec[n1,:] = model.infer_vector(i.split(' '))
        n1=n1+1
    return doc2vec

```

##### 利用word2vec模型

```python
#将文本转化成doc2vec
def word2vec(text,model,idf,is_char=False):
    '''
    将分词文本转换为字向量，将同一文本用加权平均方式求评论向量
    
    参数
    ----------------------
    text 分词后的文本内容
    model word2vec的模型
    idf 词的文档逆词频，为字典格式
    is_char 词向量模型是否为字向量模型
    
    返回
    ----------------------
    doc2vec 文档向量
    '''
    if idf==None:
        idf={}
        for i in model.wv.vocab.keys():
            idf[i]=1
    n1=0
    doc2vec=np.zeros((len(text),model.trainables.layer1_size))
    for i in text:
        tmp_vec=np.zeros((1,model.trainables.layer1_size))
        sum_weight=0
        if is_char==True:
            word_list=list(i)
        else:
            word_list=jieba.lcut(i)
        for word in word_list:
            tmp_vec=tmp_vec+model.wv[word]*idf[word]
            sum_weight=sum_weight+idf[word]
        tmp_vec=tmp_vec/sum_weight
        doc2vec[n1,:] =tmp_vec 
        n1=n1+1
    return doc2vec

```

#### 雪球评论

对雪球论坛评论数据的处理，

第一步是格式处理，

第二步是对回复别人的评论只取最后一个回复，剔除前人的讨论，例如：

`'回复@时光机在天堂口: 纯利75亿～我说的是今年目标市值1000亿～这PE怎么也是13.3啊～哪里到15了～当然我说的合理估值应该PE在15以上～对外可以参考康明斯、对内可以参考中国巨石、三一重工～都是制造业的行业翘楚～动态PE都在25左右//@时光机在天堂口:回复@伊飞2020:假使17年净利67亿，18年增长7个亿，那就是74亿，现在h股的价格偏离a股太多，将来涨上来以后也会提高潍柴的市值，这样算下来，给15倍的估值是否会有些偏高，毕竟以格力在空调行业的地位，增速也不低于潍柴的情况下，估值也只是15倍。潍柴我没你研究的深，也是今年才开始研究的，只是这个增速有些低于我预期了。当然我更看好的是潍柴未来的竞争力，将来掌握了定价权，利润进一步释放，光是股息都很喜人。'`

只取如下最后一个回复内容，即该用户发布得内容。

`'纯利75亿～我说的是今年目标市值1000亿～这PE怎么也是13.3啊～哪里到15了～当然我说的合理估值应该PE在15以上～对外可以参考康明斯、对内可以参考中国巨石、三一重工～都是制造业的行业翘楚～动态PE都在25左右'`

```python
def text_process(series):
    '''
    对文本列进行处理。
    
    参数
    ------------------
    series 文本series
    '''
    #填充NaN
    series=series.fillna('')
    #符号替换统一
    series=series.str.replace('：',':')
    
    #图片展示为空
    series=series.str.replace('（点击查看清晰图片）','【图片】')
    #超链接
    series=series.apply(lambda x:re.sub('http.*[A-Za-z0-9]','【网页链接】',x))
    
    #去除字符串两侧的空格
    series=series.str.strip()
    
    return series

def return_comment(string):
    '''
    返回雪球评论中首个回复
    
    参数
    ------------------------
    string 雪球评论
    '''
    #只有图片或者网页链接的返回空
    if string=='【网页链接】' or string=='【图片】':
        return ''
    else:
        try:
            res=re.split('.{0,2}@\w*?:',string)
            if len(res[1])>0:
                return res[1]
            elif len(res[0])>0:
                return res[0]
            else:
                return res[2]
        except IndexError as e:
            return string
```



### 模型验证

#### f1指标

```python
#f1-score
def f1_score(p_y,y):
    '''
    计算f1得分
    
    参数
    -----------------
    p_y 预测的标签值
    y 实际标签值
    
    返回值
    --------------------
    f1 f1得分
    precision 准确率
    recall 召回率
    '''
    _y=pd.Series(p_y)
    try:
        precision=sum(y[_y==1])/sum(_y==1)
    except ZeroDivisionError as e:
        precision=0
    
    try:
        recall=sum(_y[y==1])/sum(y==1)
    except ZeroDivisionError as e:
        recall=0
        
    try:
        f1=2*(precision*recall)/(precision+recall)
    except ZeroDivisionError as e:
        f1=0
        
    return f1,precision,recall

def zhibiao(ty,py):
    tp=sum(py[ty==1])
    fp=sum(py[ty==0])
    tn=sum(py[ty==0]==0)
    fn=sum(py[ty==1]==0)
    return tp,fp,tn,fn
```

#### roc画图和auc的值

```python
from sklearn.metrics import roc_curve, auc
import matplotlib.pyplot as plt

def plot_roc(y,pred_y,save_path=None,pos_label=1):
    '''
    得到roc曲线图像
    
    参数
    -----------------------
    y 真实标签值，注意标签值需要1为正标签（positive）
    pred_y 预测的标签值
    save_path roc图片保存地址,如果为None则不保存
    
    返回
    -----------------------
    roc_auc auc的面积
    '''
    fpr, tpr, thresholds = roc_curve(y, pred_y, pos_label=pos_label)
    roc_auc=auc(fpr, tpr)
    plt.figure()
    plt.plot(fpr, tpr, color='darkorange',lw=2, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('Receiver operating characteristic')
    plt.legend(loc="lower right")
    if save_path!=None:
        plt.savefig(save_path)
    plt.show()
    return roc_auc
```



#### gbdt模型调参

```python
def test_result(test=None,save_path='./'+str(model)+'result.csv',
                is_sample=False,iter_num=10,label_columns='',class_list=[1,0],
                frac=0.0015,start=30,end=60,step=30,proba_list=[0.5]):
    '''
    用来测试结果，对两个训练集分别训练，调试gbdt模型参数
    
    参数
    ----------------------
    test  测试集
    save_path 结果保存地址
    is_sample 是否抽样数据调整为真实数据比例
    iter_num 迭代次数，用于多次实验取平均值。
    label_columns 标签列的名称
    class_list 分类列表，list[0]为正，list[1]为负
    frac 为达到真实数据比例，对测试集中的正例进行抽样,frac为真实数据中正例比例
    start 模型的树的数量起始范围
    end 模型的树的数量结束范围
    step 模型的树的数量的变换步长
    proba_list 模型划分正例的阈值列表
    '''
    res=pd.DataFrame(columns=['iter_num','n','p','is_balance','precision','recall','f1','score'])
    r=0
    
    #如果数据无需抽样，则无需迭代多次进行数据抽样
    if is_sample==False:
        test_vec=doc2vec(test.分词文本,model)
        test_label=test[label_columns]
    for i in range(iter_num):
        if is_sample==True:
            theta=frac/(1-frac)
            test1=test[test[label_columns]==class_list[1]]
            test2=test[test[label_columns]==class_list[0]]
            test3=test2.sample(int(len(test1)*theta))
            test4=pd.concat([test3,test1])
            test4=test4.reset_index(drop=True)
            test_vec=doc2vec(test4.分词文本,model)
            test_label=test4[label_columns]
       
            
       
        for n in range(start,end,step):
            gbdt = GradientBoostingClassifier(n_estimators=n,max_features='log2',max_depth=2,min_samples_split=5,random_state=1)
            gbdt = gbdt.fit(train_10_vec,train_10_label)
            predict_proba=gbdt.predict_proba(test_vec)
            for p in proba_list:
                predict_label=np.zeros(len(predict_proba))
                predict_label[predict_proba[:,1]>p]=1
                py=pd.Series(predict_label)

                #precision=sum(test_label[py==1])/sum(py==1)
                #recall=sum(py[np.array(test_label==1)])/sum(test_label==1)
                f1,precision,recall=f1_score(py,test_label)
                score=gbdt.score(test_vec,test_label)
                res.loc[r,:]=[i,n,p,'Y',precision,recall,f1,score]
                r=r+1


            gbdt = GradientBoostingClassifier(n_estimators=n,max_features='log2',max_depth=2,min_samples_split=5,random_state=1)
            gbdt = gbdt.fit(train_vec,train_label)
            predict_proba=gbdt.predict_proba(test_vec)
            for p in proba_list:
                predict_label=np.zeros(len(predict_proba))
                predict_label[predict_proba[:,1]>p]=1
                py=pd.Series(predict_label)
            
                #precision=sum(test_label[py==1])/sum(py==1)
                #recall=sum(py[np.array(test_label==1)])/sum(test_label==1)
                f1,precision,recall=f1_score(py,test_label)
                score=gbdt.score(test_vec,test_label)
                res.loc[r,:]=[i,n,p,'N',precision,recall,f1,score]
                r=r+1
    res.to_csv(save_path,index=False)
    return res
```

