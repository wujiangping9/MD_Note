# NC学习笔记



## 1.提示框

### （1）错误提示框

```java
import nc.ui.pub.beans.MessageDialog;
 MessageDialog.showErrorDlg(getEntranceUI(),"提示","已经存在所有启用证明类型"); 
```

```java
 JOptionPane.showMessageDialog(new JFrame().getContentPane(),"未查询到未汇总数据!", "提示", JOptionPane.INFORMATION_MESSAGE);   
```

### （2）自定义提示框 

```java
 public class GroupComfireDialog extends HrDialog {     
 
     private UILabel label;    
  
     private UILabel label2;  
 
     private UIPanel contentPanel;    
 
     private ProofileVO[] data;    

     private LoginContext context;    
  
     public GroupComfireDialog(Container parent, String strTitle, ProofileVO[] data, LoginContext context) {        
      //构造，设置样式，传的数据    
 }    
     @Override    					 		 
     protected JComponent createCenterPanel() {             
         return getContentPanel();                                    
     }
  
     public UIPanel getContentPanel() {  
 if (contentPanel == null) {           
     contentPanel = new UIPanel();            
     contentPanel.setLayout(null);           
     contentPanel.setBounds(0, 0, 300, 140);          
     contentPanel.add(getLabel());            
     contentPanel.add(getLabel2());        }       
      return contentPanel;    }    
     public UILabel getLabel() {        
         if (label == null) {            
             label = new UILabel();            
             label.setText("当前节点中已经存在部分启用证明，是否继续引用");            
             label.setBounds(30, 20, 280, 25);
         }       
         return label;    }     
     public UILabel getLabel2() {        
         if (label2 == null) {            
             label2 = new UILabel();            
             label2.setText("其他未存在的启用证明");            
             label2.setBounds(30, 55, 280, 25);        
         }        return label2;    }     
     @Override    
     public void closeOK() {               
         //设置点击确定后做的事情        
         this.close();     
     } 
 }
```

## 2.遍历object数组 

```java
if(object.getClass().isArray()){            
    int length = Array.getLength(object);            
    AggCommissionVO[] comArr = new AggCommissionVO[length];            
    for(int i = 0;i<length;i++){                
        comArr[i] = (AggCommissionVO) Array.get(object, i);            
    }           
    getService().delete(comArr);       
} 
```

##3.获取刚刚修改完的子表那条数据

```java
DetailCommVO selectedVO =(DetailCommVO) getBillCardPanel().getBillModel(evt.getTableCode())
    .getBodyValueRowVO(evt.getRow(), DetailCommVO.class.getName());
```

##4.执行将聚合VO展现到卡片界面 

```
（1）只有一个展现到卡片界面
```

```java
getBillCardPanel().getHeadItem(PermissionVO.PK_USER).setValue("内容");
```

```
（2）少量展现，只有主表的展现或者子表的展现
```

```java
//主表展现
this.billCardPanel.getBillData().setHeaderValueVO(vo);
//子表展现
for (int i = 0; i < billModel.getRowCount(); i++) {
	billModel.setBodyRowObjectByMetaData(childrenVO()[i], i);
}
```

```
（3）大量的需要修改，主子表都有数据要展现，组装VO后，然后使用的方法
```

```java
protected void setValueAfterCalculate(Object object) {        
    if ((object == null) || (!(object instanceof AggregatedValueObject)))            
        return;        
    AggregatedValueObject aggvo = (AggregatedValueObject) object;
  getCardPanel().getBillCardPanel().getBillData().setHeaderValueVO(aggvo.getParentVO());        getCardPanel().getBillCardPanel().getBillData().loadLoadHeadRelation();        
    BillModel billModel = getCardPanel().getBillCardPanel().getBillModel();        
    if ((billModel == null) || (billModel.getRowCount() <= 0))            
        return;        
    for (int i = 0; i < billModel.getRowCount()-1; i++) {            
        billModel.setBodyRowObjectByMetaData(aggvo.getChildrenVO()[i], i);
    }        
    billModel.loadLoadRelationItemValue();    
}
```

##5.获取子表选中的行号

```java
int[] iSelectIndex = getCardPanel().getBillCardPanel().getBillTable(getCardPanel().getBillCardPanel().getCurrentBodyTableCode()).getSelectedRows();
```

##6.Object数组创建的新方法

```java
Object[] objs = new Object[] { setModel.getSelectedData() };
```

##7.审批流选择框的查询设置

```
（1）自由：-1	提交：3		未批准：0	已批准：1	审批中：2

（2）待处理：110	已处理：111		待执行：112

（3）全部：100000	已执行：102
```

```java
PFQueryParams  params = getModel().getQueryParams();
int type = params.getBillState();
```

##8.获取卡片界面的VO或AggVO和获取选中的数据的VO

```java
PermissionVO vo = (PermissionVO) getBillCardPanel()//卡片界面的VO(AggVO)
    .getBillData().getBillObjectByMetaData();
PermissionVO vo = (PermissionVO)getModel().getSelectedData()//数据库的VO(AggVO)
```

##9.根据需要修改卡片界面上的数据，保存到数据库中（多用于存在不显示在卡片界面的数据，如参照多选）

```java
//在CardForm类里面重写这个getValue方法，返回的aggvo或者vo就是要保存的
public Object getValue() {
		PermissionVO vo = (PermissionVO) getBillCardPanel()
		.getBillData().getBillObjectByMetaData();
		String names="name";
		String pk = "pk";
		vo.setPermission(names);
		vo.setInfoset(pk);
		}
		return vo;
	}
```

##10.判断字符串里面存在中文

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
 
public class Demo {
	    public static void main(String[] args) {
	        System.out.println(isContainChinese("中国China"));
	    }
	 
	    public static boolean isContainChinese(String str) {
	 
	        Pattern p = Pattern.compile("[\u4e00-\u9fa5]");
	        Matcher m = p.matcher(str);
	        if (m.find()) {
	            return true;
	        }
	        return false;
	    }
	}
```

##11.参照多选没有显示

(1)直接重写重写父类（AbstractRefModel）的matchPkData(String[] strPkValues)方法

```java
	@Override
	public Vector matchPkData(String[] strPkValues) {
		String ii = strPkValues[0];
		String[] value = ii.split(",");
		return super.matchPkData(value);
	}
```

(2)注意：

- *参照多选不能在列表界面显示，需要再定义一个字段用于存储多选参照名字，并在列表界面*

- *多选参照也不能保存，所以需要发布有参照的元数据，在建表将参照类型改成blob类型*

- *在选完时虽然会显示在卡片界面，但是保存时只保存第一个，所以要重写卡片类（HrBillFormEditor）的getValue（）方法，重新将查到的主键设置到参照编辑框内*

- 重写后的参照要在handleEvent里加上getInfoSetRefPane().setRefModel(getInfoSetRefModel())和
   		getInfoSetRefPane().setMultiSelectedEnabled(true)

```java
   	getInfoSetRefPane().setRefModel(getInfoSetRefModel());
   	getInfoSetRefPane().setMultiSelectedEnabled(true);
```

##12.查询OrgVO的接口

```java
import nc.itf.om.IAOSQueryService;
//BaseDAO查询的参数
BaseDAO.executeQuery(String sql, ResultSetProcessor processor) ；
//sql：查询语句，processor：设置返回的类型和形式
processor是在 import nc.jdbc.framework.processor的类的类型（ArrayListProcessor）
//HR多用BaseDAOManager来查询VO
OrgVO[] orgs = getBaseDAO().executeQueryVO("select pk_org,name from org_orgs where ", OrgVO[].class);

```

##13.过滤按钮

```xml
	<!-- 过滤 -->
	<bean id="FilterAction" class="nc.funcnode.ui.action.MenuAction">
		<property name="code" value="filter" />
		<property name="name" value="过滤" />
		<property name="actions">
			<list>
				<ref bean="showUnUseDataAction" />
			</list>
		</property>
	</bean>
```



```java
@Override
	public void setShowUnUseDataFlag(boolean paramBoolean) {
		this.isSealDataShow=isSealDataShow;
	}
```

##14.参照过滤

```java
//重新参照，在rest（）方法中设置
setWherePart("respath ='6007psn'");
```

```java
//在public boolean beforeEdit(BillItemEvent e)或public boolean beforeEdit(BillEditEvent e)添加
import nc.ui.pub.bill.BillEditListener2;//子表
import nc.ui.pub.bill.BillCardBeforeEditListener;//主表
	@Override
	public boolean beforeEdit(BillEditEvent e) {// 子表编辑前事件
		String key = e.getKey();
		BillItem item = getBillCardPanel().getBodyItem(DetailCommVO.PK_PSNJOB);
		if (DetailCommVO.PK_PSNJOB.equals(key)) {
			((PsnjobRefModel) ((UIRefPane) item.getComponent()).getRefModel())
					.setWherePart("过滤条件");
		}
		return true;
	}
```

##15.启用按钮

```xml
<!-- 按钮组(停用，启用 )-->
	<bean id="UseActionGroup" class="nc.funcnode.ui.action.GroupAction">
		<property name="code" value="enable" />
		<property name="name" value="启用" />
		<property name="actions">
			<list>
				<ref bean="useAction" />
				<ref bean="unUseAction" />
			</list>
		</property>
	</bean>
	<!-- 按钮组（启用） -->
	<bean id="useAction" class="nc.ui.hrhi.proofile.action.PRFEnableAction">
		<property name="model" ref="ManageAppModel" />
		<property name="enabled" value="false" />
		<property name="dataManager" ref="modelDataManager" />
	</bean>
	
	<!-- 按钮组（停用） -->
	<bean id="unUseAction" class="nc.ui.hrhi.proofile.action.PRFDisableAction">
		<property name="model" ref="ManageAppModel" />
		<property name="enabled" value="false" />
		<property name="dataManager" ref="modelDataManager" />
	</bean>
```

```java
package nc.ui.hrhi.proofile.action;

import java.awt.event.ActionEvent;

import nc.bs.framework.common.NCLocator;
import nc.itf.hrhi.proofile.IManageService;
import nc.ui.hr.uif2.action.DisableAction;
import nc.ui.hrhi.proofile.model.ProofileModelDataManager;
import nc.vo.hrhi.proofile.ProofileVO;
import nc.vo.pub.lang.UFBoolean;

public class PRFDisableAction extends DisableAction {
	private ProofileModelDataManager dataManager;

	public ProofileModelDataManager getDataManager() {
		return dataManager;
	}

	public void setDataManager(ProofileModelDataManager dataManager) {
		this.dataManager = dataManager;
	}
	
	private IManageService service;
	
	public IManageService getService(){
		if(service==null){
			service = NCLocator.getInstance().lookup(IManageService.class);
		}
		return service;
	}
	@SuppressWarnings("restriction")
	@Override
	public void doAction(ActionEvent arg0) throws Exception {
		super.doAction(arg0);
	}

	@SuppressWarnings("restriction")
	@Override
	protected boolean isActionEnable() {
		ProofileVO data = (ProofileVO) getModel().getSelectedData();
		if(data == null ){
			return false;
		}else if ((data.getFilebuilding()==null||data.getIsuse().equals(UFBoolean.FALSE))){
			return false;
		}else{
			return true;
		}
	}

	@Override
	public Object doDisable(Object vo) throws Exception {
		return getService().disable((ProofileVO) vo);
	}
	
}

```

```java
package nc.ui.hrhi.proofile.action;

import java.awt.event.ActionEvent;

import nc.bs.framework.common.NCLocator;
import nc.itf.hrhi.proofile.IManageService;
import nc.ui.hr.uif2.action.EnableAction;
import nc.ui.hrhi.proofile.model.ProofileModelDataManager;
import nc.vo.hrhi.proofile.ProofileVO;
import nc.vo.pub.BusinessException;
import nc.vo.pub.lang.UFBoolean;

public class PRFEnableAction extends EnableAction{
	private ProofileModelDataManager dataManager;

	public ProofileModelDataManager getDataManager() {
		return dataManager;
	}

	public void setDataManager(ProofileModelDataManager dataManager) {
		this.dataManager = dataManager;
	}
	
	private IManageService service;
	
	public IManageService getService(){
		if(service==null){
			service = NCLocator.getInstance().lookup(IManageService.class);
		}
		return service;
	}
	
	@Override
	public Object doEnable(Object vo) throws Exception {
		return getService().enable((ProofileVO) vo);
	}

	@SuppressWarnings("restriction")
	@Override
	public void doAction(ActionEvent arg0) throws Exception {
		super.doAction(arg0);
	}

	@SuppressWarnings("restriction")
	@Override
	protected boolean isActionEnable() {
		ProofileVO data = (ProofileVO) getModel().getSelectedData();
		if(data == null ){
			return false;
		}else if ((data.getFilebuilding()==null||data.getIsuse().equals(UFBoolean.TRUE))){
			return false;
		}else{
			return true;
		}
	}
}

```

![1565355964218](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\1565355964218.png)