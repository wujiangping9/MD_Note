1.获取当前登录人

```java
// client下获取用户信息
SessionContext.getInstance().getClientInfo().getUserid();
// private下获取用户信息
import nc.hr.utils.PubEnv;
{
	PubEnv.getPk_user(); // 获取当前登录用户主键
	UserVO userVO = 											NCLocator.getInstance().lookup(ISMUserQueryService.class).queryUserByIDOrCode(PubEnv.getPk_user(), null); // 获取用户UserVO
}
```

2.前端获取登录人信息

```js
import { getBusinessInfo } form "nc-lightapp-front"
//使用businessInfo的时候再调用，不要import进来直接执行，在组件内部执行
let businessInfo = getBusinessInfo();
console.log(businessInfo)
/*{businessDate //业务日期
    groupId //业务集团信息
    groupName //集团名称
    userId //用户id
    userName //用户名称
    userCode 用户编码
    projectCode //项目编码}*/

```

3.获取编码规则的编码

```java
1.获取自动生成的编码
    	// 编码规则
		IHrBillCode billCode = NCLocator.getInstance().lookup(IHrBillCode.class);
		// 获取集团或者全局的编码规则
		String code = billCode.getBillCode("ZPJOB", PubEnv.getPk_group(), pk_hrorg); 
		if (code == null) {
			throw new BusinessException("请设置集团或者全局的编码规则！");
		}
2.编码的回滚
        IHrBillCode billCode = NCLocator.getInstance().lookup(IHrBillCode.class);
        billCode.rollbackPreBillCode("ZPJOB",PubEnv.getPk_group(), null, param.get("code"));
```

4.获取用户的拥有的角色

```java
nc.itf.uap.rbac.IRoleManageQuery.queryRoleByUserID
```

