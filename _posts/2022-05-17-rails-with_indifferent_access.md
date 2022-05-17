---
layout: post
title: "[Rails] with_indifferent_access 使用"
date: 2022-05-17 21:40:48 +0800
categories: Rails
---

今天发现了一个`ActiveSupport::HashWithIndifferentAccess`类里的一个方法`with_indifferent_access`,
`with_indifferent_access`方法允许将hash同时使用string和symbol的方式操作hash key。
## 使用场景
  当访问一个 hash 时，如果其中的元素使用了String形式的 key，而我们使用 Symbol 形式的 key进行调用，就会无法取到值
```
hash = { 'type' => 'mobile' }
hash['type'] => "mobile"
# 用符号key会返回 nil
hash[:type] => nil

# hash 调用 with_indifferent_access 时就可以同时使用两种类型的key了
new_hash = { 'type' => 'Web', :language => 'Ruby' }
new_hash[:type] => "web"
new_hash["type"] => "web"
new_hash[:language] => "web"
new_hash["language"] => "web"
```