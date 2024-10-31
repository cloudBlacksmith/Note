# 显示镜像存放路径以验证配置是否成功
更改配置文件: `/etc/glance/glance-api.conf`
检索条件: show_image
```conf
show_image_direct_url = true
```

# 对接cinder
更改配置文件: `/etc/glance/glance-api.conf`
检索条件: default_store
```conf
default_store = cinder
```
检索条件: stores =
```conf
stores = file,http,cinder
```

更改配置文件: `/etc/cinder/cinder.conf`
检索条件: image_up
```conf
image_upload_use_cinder_backend = true
image_upload_use_internal_tenant = true
```

重启服务生效:`openstack-service restart cinder glance`
