# 【SpringBoot】SpringBoot 构建RestFul API 含单元测试

首先，回顾并详细说明一下在快速入门中使用的  

@Controller
 、  

@RestController
 、  

@RequestMapping
 注解。如果您对Spring MVC不熟悉并且还没有尝试过快速入门案例，建议先看一下快速入门的内容。 

* @Controller
 ：修饰class，用来创建处理http请求的对象 
* @RestController
 ：Spring4之后加入的注解，原来在  

@Controller
 中返回json需要  

@ResponseBody
 来配合，如果直接用  

@RestController
 替代  

@Controller
 就不需要再配置  

@ResponseBody
 ，默认返回json格式。 
* @RequestMapping
 ：配置url映射

## 一、Controller 层

package com.creditease.bsettle.crm.controller.user; import com.creditease.bsettle.crm.model.User; import com.creditease.bsettle.crm.service.UserService; import com.creditease.bsettle.crm.util.ResponseUtil; import com.creditease.bsettle.monitor.base.controller.BaseCommonQueryController; import lombok.extern.slf4j.Slf4j; import org.springframework.beans.factory.annotation.Autowired; import org.springframework.web.bind.annotation./*; import javax.servlet.http.HttpServletRequest; import java.util.HashMap; import java.util.Map; @Slf4j @RequestMapping(value = "/v1/users") @RestController public class UserControllerRestFulDemo extends BaseCommonQueryController<User>{ @Autowired UserService userService; @RequestMapping(value = "",method = RequestMethod.GET) @ResponseBody public Map<String, Object> pageList(HttpServletRequest request, @RequestParam Map<String, String> searchParams) { Map<String, Object> maps = new HashMap<String, Object>(); try { //TODO do something //maps = userService.queryPage(searchParams); } catch (Exception var4) { log.error(var4.getMessage(), var4); maps.put("retCode", Boolean.valueOf(false)); maps.put("retMessage", var4.getMessage()); } return maps; } //*/* /* 根据用户ID 查询用户信息 /* @param request /* @param id /* @return /*/ @RequestMapping(value = "/{id}",method = RequestMethod.GET) @ResponseBody Map<String,Object> findUser(HttpServletRequest request,@PathVariable Long id) { Map<String,Object> resultMap = ResponseUtil.createMap(true,"操作成功！"); try { //TODO do something //* User user = userService.findUserById(id); resultMap.put("user",user);/*/ }catch(Exception e){ log.error("findUserById is Exception !!! {} \n",e); resultMap = ResponseUtil.createMap(false,"操作失败！"+e.getMessage()); } return resultMap; } //*/* /* 修改用户信息 /* @param request /* @param user /* @param updateType 修改类型 1：修改密码 2：修改用户信息 3：修改用户状态 /* @return /*/ @RequestMapping(value="/{id}", method = RequestMethod.PUT) @ResponseBody Map<String,Object> updateUserInfo(HttpServletRequest request,@ModelAttribute User user,@PathVariable Long id, @RequestParam("updateType") int updateType){ Map<String,Object> resultMap = ResponseUtil.createMap(true,"操作成功！"); try{ //TODO do something // userService.updateUser(user,updateType,id); }catch (Exception e){ log.error("updateUserInfo is Exception !!! {} \n",e); resultMap = ResponseUtil.createMap(false,"操作失败！"+e.getMessage()); } return resultMap; } //*/* /* 根据用户ID删除用户 /* @param request /* @param id /* @return /*/ @RequestMapping(value="/{id}", method = RequestMethod.DELETE) @ResponseBody Map<String,Object> delUserById(HttpServletRequest request,@PathVariable Long id){ Map<String,Object> resultMap = ResponseUtil.createMap(true,"操作成功！"); try{ //TODO do something //userService.delete(id); }catch (Exception e){ log.error("delUserById is Exception !!! {} \n",e); resultMap = ResponseUtil.createMap(false,"操作失败！"+e.getMessage()); } return resultMap; } //*/* /* 保存用户信息 /* @param request /* @param user /* @return /*/ @RequestMapping(value = "", method = RequestMethod.POST) @ResponseBody Map<String,Object> saveUserInfo(HttpServletRequest request, @ModelAttribute User user){ Map<String,Object> resultMap = ResponseUtil.createMap(true,"操作成功！"); try{ //TODO do something //userService.save(user); }catch (Exception e){ log.error("saveUserInfo is Exception !!! {} \n",e); resultMap = ResponseUtil.createMap(false,"操作失败！"+e.getMessage()); } return resultMap; } }

## 二、View 层

### 1. 根据查询条件获取user列表

$.ajax({ url: '../v1/users', async: false, type: 'GET', dataType: 'json data: { //TODO } , success: function(data) { //TODO } });

OR 

$('/#userTable').bootstrapTable({ method: 'GET', url: '../v1/user', dataType: 'json', pagination: true, pageList: [5,10,15,20], pageNumber: 1, pageSize: 5, //singleSelect: true, clickToSelect: true, sidePagination: 'server', queryParams: queryParams, locale: 'zh-CN', // 略 })

### 2. 获取ID为1 的用户

$.ajax({ url: '../v1/user/1', type: 'GET', dataType: 'json', data: { //TODO }, success: function(data) { //TODO } });

### 3. 更新用户ID为1 的用户信息

$.ajax({ url: '../v1/user/1', type: 'PUT', dataType: 'json', data: { //TODO new user data }, success: function(data) { //TODO } });

### 4. 删除用户ID为1的用户

$.ajax({ url: '../v1/user/1', type: 'DELETE', dataType: 'json', data: { //TODO other param }, success: function(data) { //TODO } });

## 三、测试用例

package com.creditease.bsettle.crm; import org.junit.Before; import org.junit.Test; import org.junit.runner.RunWith; import org.springframework.beans.factory.annotation.Autowired; import org.springframework.boot.test.context.SpringBootTest; import org.springframework.test.context.junit4.SpringJUnit4ClassRunner; import org.springframework.test.context.web.WebAppConfiguration; import org.springframework.test.web.servlet.MockMvc; import org.springframework.test.web.servlet.RequestBuilder; import org.springframework.test.web.servlet.request.MockMvcRequestBuilders; import org.springframework.test.web.servlet.setup.MockMvcBuilders; import org.springframework.web.context.WebApplicationContext; import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print; import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content; import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status; //*/* /* @author mengfanzhu /* @Package com.creditease.bsettle.crm /* @Description: /* @date 5/19/17 10:40 /* /*/ @RunWith(SpringJUnit4ClassRunner.class) @SpringBootTest(classes = CrmApplication.class) @WebAppConfiguration public class UserServiceRestTest { @Autowired private WebApplicationContext wac; private MockMvc mvc; @Before public void setUp() throws Exception { // mvc = MockMvcBuilders.standaloneSetup(new UserControllerRestFulDemo()).build(); mvc = MockMvcBuilders.webAppContextSetup(wac).build(); } @Test public void getUserList() throws Exception { // 1、get查一下user列表 RequestBuilder request = MockMvcRequestBuilders.get("/v1/users") .header("auth", "false") .param("pageNumber", "2") .param("pageSize", "10"); mvc.perform(request) .andDo(print()) //print request and response to Console .andExpect(status().isOk()) .andExpect(content().contentType("application/json;charset=UTF-8")); } @Test public void postUser() throws Exception { // 2、post提交一个userRequestBuilder RequestBuilder request = MockMvcRequestBuilders.post("/v1/user") .header("auth","false") .param("isModifyPassword","N") .param("status","O") .param("userName","testName"+System.currentTimeMillis()) .param("userLoginPassword","aaaa1234") .param("mobile","13322221111") .param("email","fjksdfj@11.com") .param("crmEnterpriseId","1"); mvc.perform(request) .andDo(print()); } @Test public void getUser() throws Exception { // get获取user列表，应该有刚才插入的数据 RequestBuilder request = MockMvcRequestBuilders.get("/v1/user?mobile=13322221111") .header("auth","false") .param("pageNumber", "1") .param("pageSize", "10");; mvc.perform(request) .andDo(print()) .andExpect(status().isOk()); } @Test public void getUserById() throws Exception { // get一个id为1的user RequestBuilder request = MockMvcRequestBuilders.get("/v1/user/1") .header("auth","false"); mvc.perform(request) .andDo(print()); } @Test public void putUserInfoById() throws Exception { // put修改id为1358的user RequestBuilder request = MockMvcRequestBuilders.put("/v1/user/1358") .header("auth","false") .param("updateType","2") .param("userName", "测试终极大师") .param("email", "11111@qq.com"); mvc.perform(request) .andDo(print()); } @Test public void delUserInfoById() throws Exception { //del删除id为1358的user RequestBuilder request = MockMvcRequestBuilders.delete("/v1/user/1358") .header("auth","false"); mvc.perform(request) .andDo(print()); } }

 


