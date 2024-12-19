office: 10.19.110.164
home: 192.168.4.57

change memcache, rabbit, mysql and nova ip
sudo nano /etc/memcached.conf
sudo nano /etc/rabbitmq/rabbitmq-env.conf
sudo nano /etc/nova/nova.conf
sudo nano /etc/mysql/mariadb.conf.d/99-openstack.cnf


restart
sudo systemctl restart rabbitmq-server
memcache
mysql
apache2


logs
sudo cat /var/log/apache2/error.log


sudo keystone-manage bootstrap --bootstrap-password 0402 \
  --bootstrap-admin-url http://controller.local:5000/v3/ \
  --bootstrap-internal-url http://controller.local:5000/v3/ \
  --bootstrap-public-url http://controller.local:5000/v3/ \
  --bootstrap-region-id RegionOne



export OS_USERNAME=admin
export OS_PASSWORD=0402
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_URL=http://controller.local:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2


PLACEMENT

sudo mysql -u root -p
CREATE DATABASE placement;
GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'localhost' IDENTIFIED BY '0402';
GRANT ALL PRIVILEGES ON placement.* TO 'placement'@'%' IDENTIFIED BY '0402';

openstack user create --domain default --password-prompt placement
openstack role add --project service --user placement admin
openstack service create --name placement --description "Placement API" placement
openstack endpoint create --region RegionOne placement public http://controller.local:8778
openstack endpoint create --region RegionOne placement internal http://controller.local:8778
openstack endpoint create --region RegionOne placement admin http://controller.local:8778


openstack server create --flavor m1.tiny --image 85e78d3d-f1dc-4c44-8f64-6bb142759fcb --network private --key-name mykey cirros-instance


openstack endpoint create --region RegionOne volume public http://controller.local:8776/v1/%\(project_id\)s
openstack endpoint create --region RegionOne volume internal http://controller.local:8776/v1/%\(project_id\)s
openstack endpoint create --region RegionOne volume admin http://controller.local:8776/v1/%\(project_id\)s

openstack endpoint create --region RegionOne volumev3 public http://controller.local:8776/v3/%\(project_id\)s
openstack endpoint create --region RegionOne volumev3 internal http://controller.local:8776/v3/%\(project_id\)s
openstack endpoint create --region RegionOne volumev3 admin http://controller.local:8776/v3/%\(project_id\)s


GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' IDENTIFIED BY '0402';
GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' IDENTIFIED BY '0402';