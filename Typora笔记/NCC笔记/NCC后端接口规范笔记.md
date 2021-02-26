## 1.后端接口转化方法

（1）直接继承NCCAction，可以向前端抛出异常

```java
import nccloud.web.action.NCCAction;
public class XXXXXAction extends NCCAction {
    @Override
	public <T> Object execute(IRequest paramIRequest, T paramT) throws Exception {
        ParamClass param = (ParamClass)paramT;
		return null;
	}
    
    /**
    *重写转换的类
    */
    @Override
	protected Class<ParamClass> getParaClass() {
		return ParamClass.class;
	}
    
    /**
    *可以在这里写内部类，也可以新建一个包专门写参数转换的类
    */
    private class ParamClass {
        private String xx;
        ....添加get和set方法
    }
    
}
```

（2）实现接口ICommonAction，只能抓异常

```java
import nccloud.framework.web.action.itf.ICommonAction;
public class CancelDefaultCardAction implements ICommonAction {
    @Override
	public Object doAction(IRequest paramIRequest) {
        InterviewsetVO[] bills = this.queryBills(queryParam);
        return null;
    }
    
    /**
    *自己编写Json转类的方法
    */
    private PageQueryInfo getQueryParam(IRequest paramIRequest) {
        String strRead = paramIRequest.read();
        PageQueryInfo queryParam = JsonFactory.create().fromJson(strRead, PageQueryInfo.class);
        return queryParam;
	}
}
```

建议：如果存在参数大多数相同，可以定义一个公共的参数类进行转换

## 2.新增修改转vo方法

（1）表单数据转换成对应的单表vo类

```java
import nccloud.framework.web.ui.pattern.form.Form;
import nccloud.framework.web.ui.pattern.form.FormOperator;
import nccloud.framework.web.convert.pattern.FormConvertor;
// 可以继承NCCAction转成Form，也可以使用自己写的方法转成Form，例子在上面已经写出（建议继承NCCAction）
public class SaveZpjobAction extends NCCAction {
    @Override
	public <T> Object execute(IRequest request, T para) throws Exception {
		// 获得form对象
		Form form = (Form) para;
		FormConvertor ope = new FormConvertor();
		ZPJobVO vo = ope.fromForm(ZPJobVO.class, form);
		ZPJobVO result = null;
        // 设置集团
		vo.setPk_group(SessionContext.getInstance().getClientInfo().getPk_group());
		String pk = vo.getPk_job();
		IZpjobManageService service = ServiceLocator.find(IZpjobManageService.class);
        // 判断新增还是修改，并修改状态
		if (pk == null || "".equals(pk)) {
			vo.setStatus(VOStatus.NEW);
			result = service.insertZPJobVOs(vo);
		} else {
			vo.setStatus(VOStatus.UPDATED);
			result = service.updateZPJobVOs(vo);
		}
        // 使用FormOperator转成Form返回给前端(保存后是浏览态需要)
		FormOperator convertor = new FormOperator("60700401_zpjob");
		Form retForm = convertor.toForm(result);
		Translator translator = new Translator();
		translator.translate(retForm);
		Map<String, Object> res = new HashMap<String, Object>();
		res.put("form", retForm);
		return res;
	}

	@Override
	protected Class<Form> getParaClass() {
		return Form.class;
	}
}
```

（2）表单数据转换成对应的主子表vo类

```java
/**
 * 主子表保存
 * 《前端传的参数以数组表单格式传，主表和子表并列传递,数组的第一个传的是主表Form[0]
 * 第二个Form[1]，存放子表的Form数据，多子表也可以继续用Form[2]继续添加，前端需要
 * 按顺序依次存放，可以保证后端转换的准确
 */
public class PublishSaveAction extends NCCAction {

	@Override
	public <T> Object execute(IRequest request, T para) throws Exception {
        // 1、获取AGGVO （request转换主子VO）
        Form[] form = (Form[]) para;
        BillCardOperator billCardOperator = new BillCardOperator();
        FormConvertor ope = new FormConvertor();
        // 将第一个主表Form转成主表VO
        PublishVO parentVO = ope.fromForm(PublishVO.class, form[0]);
        // 获取子表ROWs数据
        Row[] rows = form[1].getModel().getRows();
        ChannelVO[] subVOs = new ChannelVO[rows.length];
        // 循环转成Form，并依次转换成子表vo放入子表数组中
        for (int i = 0; i < rows.length; i++) {
            Form subForm = new Form();
            GridModel subGrid = new GridModel();
            subGrid.add(rows[i]);
            subForm.setModel(subGrid);
            ChannelVO subVO = ope.fromForm(ChannelVO.class, subForm);
            subVOs[i] = subVO;
        }
        // 组装AggVO
        AggPublishVO vo = new AggPublishVO();
        vo.setParentVO(parentVO);
        vo.setChildrenVO(subVOs);
        // 判断状态进行默认值处理
        this.doBefore(vo);
        // 2、调用单据的保存动作脚本（savebase），得到保存后结果
        AggPublishVO rtnObj = this.callActionScript(vo);
        // 3、处理返回结果（包含功能：根据模板转换前端BillCard，参照翻译，显示公式处理）
        Object billcard = billCardOperator.toCard(rtnObj);
        // 4、返回结果到前端
        return billcard;
	}
	/**
	 * 判断新增或修改，进行保存前端vo默认值调整以及状态修改等操作
	 * @param vo
	 */
	private void doBefore(AggPublishVO vo) {
        .....
	}

	@Override
	protected Class<Form[]> getParaClass() {
		return Form[].class;
	}
}

```

## 3.常用到的增删改查方法使用

（1）查询接口编写

​		1.列表查询

```java
// 已编写的工具类
import nc.ui.hrzpm.utils.VOsToTemplateUtils;
// 1.查询列表界面，并有分页信息<AbstractDemandAction是自己定义继承NCCAction抽象类的，用来格式化前端参数的>
public class DemandQueryListAction extends AbstractDemandAction {
	@Override
	public <T> Object execute(IRequest request, T paramT) throws Exception {
		QryParamVO qryParam = (QryParamVO) paramT;
		String where = " pk_org = '" + qryParam.getPk_org() + "' ";
		String[] allPks = CommonUtil.getQryService().queryPksByCondition(where);
        // 最后一个pageCode参数可以前端传，也可后端获取或写死
		Grid grid = VOsToTemplateUtils.toGrid(allPks, DemandVO.class, qryParam.getPageInfo(), "pageInfo");
		return grid;
	}
}

```

​		2.卡片查询

```java
// 2.查询卡片界面
import nccloud.framework.web.ui.pattern.billcard.BillCardOperator;

public class DemandQueryCardAction extends AbstractDemandAction {

	@Override
	public <T> Object execute(IRequest request, T paramT) throws Exception {
        QryParamVO qryParam = (QryParamVO) paramT;
        AggDemandVO vo = CommonUtil.getQryService().queryDemandVOByPk(qryParam.getPk());
        Form form = VOsToTemplateUtils.VOs2Form(vo,"pageCode");
		return form;
	}
    // 如果是主子表的，可使用如下转换
	private BillCard transBillCard(AggPublishVO aggvo) {
		if (aggvo == null) {
			return null;
		}
		BillCardOperator operator = new BillCardOperator();
        // BillCardOperator(String pagecode)
        // BillCardOperator(String templetid, String pagecode, BillCard card)
        BillCard billCard = operator.toCard(aggvo);
        // 暂时未测试是否需要翻译，等后续测试
        Translator translator = new Translator();
		translator.translate(billCard);
		return billCard;
	}
}
```

（2）保存【新增修改】接口编写

```java
前端数据转换成vo的方法
    1.表单转vo
        import nccloud.framework.web.convert.pattern.FormConvertor
        FormConvertor ope = new FormConvertor();
		Form form = (Form)paraT;
        PublishVO parentVO = ope.fromForm(PublishVO.class, form);
	2.表格转vo
        import nccloud.framework.web.convert.pattern.GridConvertor
        GridConvertor ope = new GridConvertor();	
		Grid grid = (Grid)paraT;
        PublishVO parentVO = ope.fromGrid(PublishVO.class, grid);
	3.表单-表格(子表)-表单(孙表<可不要>)转AggVO
        import nccloud.framework.web.convert.pattern.BillCardConvertor
        BillCardConvertor ope = new BillCardConvertor();
       	BillCard billCard = (BillCard)paraT;
		// info用来描述grid的信息,可前端传转化，或者自己组装（填写head/body，以及head的class和body的class）
		CardAreaInfo[] infos;
		AggPublishVO aggVO = ope.fromBillCard(AggPublishVO.class, billCard, infos);
	4.表格主子表转AggVO
        import nccloud.framework.web.convert.pattern.BillGridConvertor
        BillGridConvertor ope = new BillGridConvertor();
       	BillGrid billGrid = (BillGrid)paraT;
		// info用来描述grid的信息,可前端传转化，或者自己组装（填写head/body，以及head的class和body的class）
		CardAreaInfo[] infos;
		AggPublishVO aggVO = ope.fromBillCard(AggPublishVO.class, billGrid, infos);
		
        
```

1.普通AggVO保存

```java
public class DemandSaveAction extends NCCAction {

	@Override
	public <T> Object execute(IRequest paramIRequest, T paramT) throws Exception {
        BillCard billCard = (BillCard)paramT;
        CardAreaInfo[] infos = new CardAreaInfo[2];
        // 填写表头信息
        CardAreaInfo headInfo = new CardAreaInfo();
        headInfo.setAreacode("60700403_List");
        headInfo.setClazz(DemandVO.class);
        headInfo.setPosition(CardPosition.Head);
        headInfo.setGrandSon(false);
        // 填写表头信息
        CardAreaInfo bodyInfo = new CardAreaInfo();
        bodyInfo.setAreacode("60700403_List");
        bodyInfo.setClazz(DemandVO.class);
        bodyInfo.setPosition(CardPosition.Head);
        bodyInfo.setGrandSon(false);
        // 组装
        infos[0] = headInfo;
        infos[1] = bodyInfo;
        BillCardConvertor ope = new BillCardConvertor();
        AggDemandVO aggVO = ope.fromBillCard(AggDemandVO.class, billCard, infos);
        aggVO = CommonUtil.getManageService().save(aggVO);
        billCard = ope.toBillCard(aggVO,infos);
		return billCard;
	}

}
```

2.审批流AggVO保存

```java

```

（3）删除接口编写

```java
public class DemandDeleteAction extends AbstractDemandAction {

	@Override
	public <T> Object execute(IRequest paramIRequest, T paramT) throws Exception {
		QryParamVO qryParamVO = (QryParamVO) paramT;
        String[] pks = qryParamVO.getPks();
        CommonUtil.getManageService().deleteByPks(pks);
		return null;
	}

}
```



## 4.审批流方法的使用例子

(1) 审批流节点配置

【1】单据类型管理

![image-20200727172824108](C:\Users\MSI-PC\AppData\Roaming\Typora\typora-user-images\image-20200727172824108.png)

【2】单据动作管理

| 序号 | 动作选择 | 动作代码   | 动作名称 | 动作类型 | 执行前提示 | 执行约束检查 | 动作可配置 | 进行驱动配置 |
| ---- | -------- | ---------- | -------- | -------- | ---------- | ------------ | ---------- | ------------ |
| 1    | 保存     | SAVE       |          | 10       |            | 否           | 否         | 否           |
| 2    | 保存     | SAVEBASE   |          | 31       |            | 是           | 否         | 否           |
| 3    | 其他     | UNAPPROVE  |          | 12       |            | 否           | 否         | 是           |
| 4    | 其他     | UNSAVEBILL |          | 13       |            | 否           | 是         | 是           |
| 5    | 其他     | APPROVE    |          | 11       |            | 否           | 否         | 否           |
| 6    | 其他     | DELETE     |          | 30       |            | 否           | 否         | 否           |

待补充

## 5.参照编写

## 6.NCC主键生成方法

```java
import nc.jdbc.framework.generator.SequenceGenerator;
import nc.jdbc.framework.util.SQLHelper;
// 获取集团编码标识
String corpPk = SQLHelper.getCorpPk();
// 生成需要的主键
pks = (new SequenceGenerator(this.dataSource)).generate(corpPk, vos.length);
/********************************************* 备注 **************************************/
// 1.可以选择根据数据源生成主键或者不根据数据源生成（SequenceGenerator的构造）
(1) new SequenceGenerator();
// 就会默认获取登录用户的数据源
ds = InvocationInfoProxy.getInstance().getUserDataSource(); // 获取用登录用户的数据源
(2) new SequenceGenerator(String dataSource);
// 自定义数据源
// 2.生成主键的方法(SequenceGenerator提供的四种生成主键的方法)
opr = new SequenceGenerator();
(1) opr.generate()
     String groupNumber = InvocationInfoProxy.getInstance().getGroupNumber(); // 使用的是此集团标识
(2) opr.generate(String pk_corp) // 使用的是《pk_corp》集团标识
(3)opr.generate(String pk_corp,int amout) // 生成主键大于一个时，amout为生成的数量
(4)opr.generate(int amout) // 使用默认参数生成amout数量的主键
```

## 7.NCC多语的获取

```java
1.多语的获取
NCLangResOnserver.getInstance().(String productCode, String resId);
// 多语根据的jar包路径
{HOME}\modules\uapbs\lib\pubuapbs_multilangLevel-1.jar
2.异常抛出到前端
import nccloud.framework.core.exception.ExceptionUtils
例子: ExceptionUtils.wrapException(ex);
```

## 8.NCC后端对数据实现的增删改查接口使用

```java
/**
*1.用元数据方式实现增删改查
*/
    private static IMDPersistenceService getMDPersistenceService() {
            return MDPersistenceService.lookupPersistenceQueryService();
            return MDPersistenceService.lookupPersistenceService();
        }
/**
*2.用模板方式实现增删改查
*/
    public SimpleDocServiceTemplate getServiceTemplate() {
        if (serviceTemplate == null) {
            serviceTemplate = new SimpleDocServiceTemplate(MID);
        }
        return serviceTemplate;
    }
/**
*3.批量对数据增删改查
*/
	public HrBatchService getBatchService() {
		if (batchService == null) {
			batchService = new HrBatchService(MID);
		}
		return batchService;
	}
/**
*4.批量数据操作处理，一个方法中存在新增修改删除操作，根据的是status状态太来判断
*/
		BatchVOTool<ApplysetVO> tool = new BatchVOTool<ApplysetVO>(ApplysetVO.class);
		BatchOperateVO batchVO = tool.getBatchOperateVO(vos);
		BatchOperateVO savedBatchVO = 																ServiceLocator.find(IApplysetService.class).batchSaveCurrtype(batchVO,
				request.read());
		ApplysetVO[] saveVOs = tool.getVOsByBatchVO(savedBatchVO);


ApplysetServiceImpl extends nc.bs.bd.baseservice.md.BatchBaseService<ApplysetVO>
```

## 9.审批流执行脚本类

```java
import nc.bs.pub.pf.pfframe.PFRunClass;
    public Object runComBusi(PfParameterVO pfVo, UFBoolean isBefore, String actionName) throws BusinessException {
        Logger.debug("动作脚本执行开始.....");
        BusiclassVO busiClassVo = this.findActionScriptClass(actionName, isBefore, pfVo);
        String strClassNameNoPackage = busiClassVo.getClassname().trim();
        String className = "nc.bs.pub.action." + strClassNameNoPackage;
        pfVo.m_actionName = actionName;
        Object instOfActionscript = PfUtilTools.findBizImplOfBilltype(busiClassVo.getPk_billtype(), className);
        IBillActionClassRunListener listener = this.getBillActionClassRunListener(busiClassVo.getPk_billtype());
        if (listener != null) {
            listener.beforeActionExecute(pfVo);
        }

        Object actionReturnObj = ((IPfRun)instOfActionscript).runComClass(pfVo);
        if (listener != null) {
            listener.afterActionExecute(pfVo);
        }

        Logger.debug("动作脚本执行完成....." + instOfActionscript);
        return actionReturnObj;
    }
```

