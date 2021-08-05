---
layout:     post
title:      【Spring Boot | Jpa】Spring Boot 2.0 JPA 实现分页（简单查询分页、复杂查询分页）
subtitle:   【Spring Boot | Jpa】Spring Boot 2.0 JPA 实现分页（简单查询分页、复杂查询分页）
date:       2019-12-12 17:16:40
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Spring Boot
    - 数据库
---

>【Spring Boot | Jpa】Spring Boot 2.0 JPA 实现分页（简单查询分页、复杂查询分页）

# 【Spring Boot | Jpa】Spring Boot 2.0 JPA 实现分页（简单查询分页、复杂查询分页）


**目录**

[一、简单分页（只有一个查询条件）](#%E4%B8%80%E3%80%81%E7%AE%80%E5%8D%95%E5%88%86%E9%A1%B5%EF%BC%88%E5%8F%AA%E6%9C%89%E4%B8%80%E4%B8%AA%E6%9F%A5%E8%AF%A2%E6%9D%A1%E4%BB%B6%EF%BC%89)

[二、多条件查询分页](#%E4%BA%8C%E3%80%81%E5%A4%9A%E6%9D%A1%E4%BB%B6%E6%9F%A5%E8%AF%A2%E5%88%86%E9%A1%B5)

----

# 一、简单分页（只有一个查询条件）

* 在Repository层将查询语句的返回值类型设置为为Page类型，查询参数中加入**Pageable pageable**,如：
@Repository public interface SshRepository extends JpaRepository<SshDao, Integer> { @Query("select s from ssh s where s.userId = :userId") Page<SshDao> selectAllByUserId(@Param("userId") Integer userId, Pageable pageable); }

* 在Service层中实例化Pageable对象，并指定currentPage（当前页）、pageSize（每页最大容量），其中PageRequest.of为Spring Boot 2.0的方法，之前版本为new PageRequest(),如：
public ResultDao getSsh(Integer userId, Integer currentPage, Integer pageSize) { // spring boot 2.0推荐写法 Pageable pageable = PageRequest.of(currentPage,pageSize); // spring boot 2.0 以前，2.0版本也适用，但是2.0版本推荐使用上面的方式 // Pageable pageable = new PageRequest(currentPage,pageSize); return ResultUtil.unitedResult(ResultEnum.SUCCESS,sshRepository.selectAllByUserId(userId, pageable)); }

* 在Controller层设置相应的接口，由于currentPage规定从0开始，而前端通常返回的是从1开始，需要同步一下
@GetMapping("/ssh") public ResultDao getSsh(@PathParam("userId") Integer userId, @PathParam("currentPage") Integer currentPage, @PathParam("pageSize") Integer pageSize) { // 同步前端传回的当前页参数 currentPage = currentPage - 1; return cloudServerService.getSsh(userId, currentPage, pageSize); }

# 二、多条件查询分页

多条件查询采用的是以元模型概念为基础的Criteria 查询方法

* Repository层继承**JpaSpecificationExecutor<实体名>**，并将返回类型改为Page类型，如：
@Repository public interface CloudServerRepository extends JpaRepository<CloudServerDao, Integer>,JpaSpecificationExecutor<CloudServerDao> { }

* 在Service层构建Specification方法，具体实现见代码，同样Spring Boot 2.0和Spring Boot 2.0之前的方法有差异，简单介绍一下各字段的含义：

* root：查询根，指实体(此处为CloudServerDao)，root.get("userId")为获取实体(此处为CloudServerDao)中的字段userId，第二个userId为函数的参数
* Predicate：定义查询条件。Predicate 对象通过调用CriteriaBuilder的条件方法( equal，notEqual， gt， ge，lt， le，between，like等)创建，具体方法自行搜索

**spring boot 2.0推荐写法**
使用repository的findAll(specification, pageable)查询即可
// 代码通过userId和key两个条件进行查询 public ResultDao getServer(String key, Integer userId, Integer currentPage, Integer pageSize) { Pageable pageable = PageRequest.of(currentPage,pageSize); Specification<CloudServerDao> specification = (Specification<CloudServerDao>) (root, query, criteriaBuilder) -> { List<Predicate> list = new ArrayList<>(); // 第一个userId为CloudServerDao中的字段，第二个userId为参数 Predicate p1 = criteriaBuilder.equal(root.get("userId"),userId); list.add(p1); if (!key.equals(null)) { // 此处为查询serverName中含有key的数据 Predicate p2 = criteriaBuilder.like(root.get("serverName"),"%"+key+"%" ); list.add(p2); } return criteriaBuilder.and(list.toArray(new Predicate[0])); }; return cloudServerRepository.findAll(specification, pageable);

**↓ Spring Boot 2.0之前的写法，Spring Boot 2.0也适用，但是2.0版本推荐使用上面的方式 ↑**

Specification<CloudServerDao> specification = new Specification<CloudServerDao>() { public Predicate toPredicate(Root<CloudServerDao> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) { List<Predicate> list = new ArrayList<>(); Predicate p1 = criteriaBuilder.equal(root.get("userId"),userId); list.add(p1); if (!key.equals(null)) { Predicate p2 = criteriaBuilder.like(root.get("serverName"),"%"+key+"%" ); list.add(p2); } return criteriaBuilder.and(list.toArray(new Predicate[0])); } }; return cloudServerRepository.findAll(specification, pageable);

* 在Controller层设置和简单查询的配置一致
@GetMapping("/ssh") public ResultDao getSsh(@PathParam("userId") Integer userId, @PathParam("currentPage") Integer currentPage, @PathParam("pageSize") Integer pageSize) { // 同步前端传回的当前页参数 currentPage = currentPage - 1; return cloudServerService.getSsh(userId, currentPage, pageSize); }

 


