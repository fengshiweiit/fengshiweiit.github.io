---
title: Summarize Jpa
date: 2018-01-24 16:26:42
tags:
---
# <center>Jpa中遇到的知识点总结</center>
---
## 1.jpa实现limit的方法（自己定义一个pageable）
    final int currentPage = 0;
    final int pageSize = 5000;
    Sort sort = new Sort(Sort.Direction.DESC, "createTime");
    Pageable pageable = new PageRequest(currentPage, pageSize, sort);
    Page<Order> page = orderRepository.findAll(spec, pageable);
    List<Order> list = page.getContent();
---
## 2.jpa实现limit的方法2
    //这里必须加by
    例：userRepository.findTop10By();
---
## 3.jpa的一级缓存
    //jpa的查询方法生效后 默认会缓存这个查询结果
    for (Integer organizationId : listMap.keySet()) {
            OrganizationManager organizationManager = organizationManagerRepository.findById(organizationId);
            if (organizationManager == null){
                log.error("组织不存在");
                throw new MessageException("组织不存在");
            }
            //这里会循环查询到resources
            List<Resource> resources = userOrganizationManagerRoleRepository.findResourceByUserIdAndOrganizationId(userId, organizationId);
            //清一级缓存
            entityManager.clear();
            List<Resource> list = buildTree(resources);
            //对resource进行了操作，如果不清一级缓存会导致查询的结果还是上一次循环的结果
            map1.put(organizationId + organizationManager.getType(), list);
    }
    