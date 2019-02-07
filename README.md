Group 2 - Elasticon:


Code:
-------------------------------------------------
group2/controller_1.py and group2/controller_2.py: RYU Controller app with Elasticon Protocol.
group2/load_balancer.py: Load Balancer App for DB access and migration decision making.
group2/multi_ctlr_topo.py: Topology file for Mininet.


Steps to run:
--------------------------------------------------
1) Start mongodb using: sudo mongod
2) Launch Controller 1 using:
ryu-manager --verbose --ofp-listen-host <IP address of controller 1> controller_1.py
3) Launch Controller 2 using:
ryu-manager --verbose --ofp-listen-host <IP address of controller 2> controller_2.py
4) Update IP addresses of the two remote controllers at net.addController() in mininet topology file.
5) Launch mininet for remote controllers with the topology file:
sudo mn --custom multi_ctlr_topo.py --controller=remote --topo mytopo


Steps to verify:
---------------------------------------------------
1) Open a python shell and connect to the db using

   client = MongoClient('localhost', 27017)   db = client.elastiCon 
   controllers = db.controllers 
   flags = db.flags   gen_id = db.gen_id    cmf = db.cmf 

2) Monitor the Packet counts at both controllers using
 >>> controllers.find_one({'id':'1'})
 >>> controllers.find_one({'id':'2'})

3) Initiate a ping in the mininet
   mininet> h1 ping h2 -i 3

   Here we use -i 3 so that packet counts can be clearly monitored.

4) Observe that, when the count reaches 150, the switch is migrated and the controller                ceases to receive further packetINs. Instead, the other controller's packetIN resets and starts to increment.

5) Between multiple runs ensure to drop the documents of the db using:
 >>> controllers.drop()
 >>> flags.drop()
 >>> gen_id.drop()
 >>> cmf.drop()




