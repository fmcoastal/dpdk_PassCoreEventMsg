# dpdk_PassCoreEventMsg



**** What this code does:


This code uses DPDK's eventdev code to send a message from one core to another
      core.  The code was developed on a CN96xx CRB.  The code itself sets up 
      each core to have a single port, and each port to have a single queue.  
      To start the "loop" function, a single event message is pushed int the 
      queue of the first core. From there, each core pushed ths message/event 
      to the next core.  (I use a simple core-NextCore table to map where the 
      message should be sent.)  At the bottom of this doc, you can see the 
      output of the program.  
        

**** How Code Is Constructed:

The dpdk_PassCoreEvnetMsg is dpdk code built on top of the "test harness code".  
      So, in the fs_core.c file, you will find "Implemented Functions".  The 
      core test is run by adding a '-t 5' to the dpdk application test line. 

          
        // struct to hold test function parameters
        struct test_mode_struct {                       "Implemented Function"    
                 int   (*setup)(void *);                  core_setup()         
                 int   (*main_loop)(void *);              core_loop()
                 int   (*print_results)(void *);          core_print()
                 int   (*cleanup)(void *);                core_cleanup()
                 void  (*description)(void);              core_description()
         };

**** To Get Help Output:

      $cd /home/fsmith/depot/dpdk_PassCoreEvnetMsg ./build/dpdk_PassCoreEvnetMsg
                            

**** Build Notes
    rootfs:        Ubuntu 18.04 rootfs
    Kernel Image:  SDK-10.3.2.0-PR11
    DPDK version:  dpdk-20.02
    Code was natively compiled 


**** Download dpdk_test_app

 - From github

   * cd to the directory where you want the "dpdk_PassCoreEventMsg" directory 
     created.
   * execute the following command to download the Project:

      $ git clone https://github.com/fmcoastal/dpdk_PassCoreEventMsg.git


 - From grok.fae.com
   * go to grok.faelab.com/codeclips/dpdk_PassCoreEventMsg
   * download dpdk_PassCoreeventMsg.tgz
   * cd to the directory where you want the "dpdk_PassCoreEventMsg" directory 
     created.
   * transfer  dpdk_PassCoreeventMsg.tgz  to your current directory
   * execute the following command to untar the file 

      $ tar -xf dpdk_PassCoreEventMsg.git

**** To Build 

  -- Standalone Environment 
  
   * cd to the dpdk_PassCoreEventMsg directory 
   * execute make 

      $ make

   * if successful,  the program dpdk_PassCoreEventMsg can be found in 

      ./build/dpdk_PassCoreEventMsg

   ** You may need the following Environment variables set:

      $ export RTE_TARGET=build
      $ export RTE_KERNELDIR=/home/fsmith/linux
      $ export RTE_SDK=/home/fsmith/dpdk/dpdk-20.02


*** to run

    ** Switch user to root
      
      $ sudo su

    ** execute the following scripts:
     
      # ./huge_mem_cfg
      # ./dpdk_cfg
      # ./h_fixes

      the scripts only need to be run once at each boot. User should review the 
      contents of these scripts.   

      huge_mem_cfg:  will check huge mem config and will assign & mount if 
                     huge memory is not present and/or mounted
      dpdk_cfg:      binds the necessary hardware devices to vfio-pci so dpdk
                     can attach to them.
      h_fixes:       As of my SDK/dpdk releases,  resources for eventdev have 
                     to be created, cleared and initialized 



      sudo ./build/dpdk_PassCoreEvnetMsg  -c 0x0f -w 0002:0e:00.0,xae_cnt=32768  -- -t 5

      PCIe device 0002:0e:00.0 is the SSO.  See DPDK documentation for xae_cnt parameter 


  -- Buildroot Environment 

     >> does not work becasue I cannot figure out the Linker libraries
         to add, and you cannot use gcc to link. 


Other Items:
    dpdk tools like dpdk_devbind.py can be found in <DPDK_RELEASE>/usertools/dpdk-*


-------------------------------------------------------------------
   Program execution output

-------------------------------------------------------------------

fsmith@ubuntu:~/depot/dpdk_PassCoreEvnetMsg$ sudo su
[sudo] password for fsmith:
RTE_TARGET=build
RTE_KERNELDIR=/home/fsmith/linux
RTE_SDK=/home/fsmith/dpdk/dpdk-20.02
root@ubuntu:/home/fsmith/depot/dpdk_PassCoreEvnetMsg# ./huge_mem_cfg
******************
* Before
******************
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
HugePages_Total:      16
HugePages_Free:       15
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:     524288 kB

******************
* Test if /huge exits.  Create if not present
******************
 file /huge exists

******************
* test if hugetlbs has been mounted to /huge
******************
--- TST_MOUNT:   cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=512M)
-- nodev has been mounted to /huge --

******************
* Test if pages in the pool.  add if none present
******************
--- ALLOCATE:  HugePages_Total:      16
--- HUGEPAGES_TOTAL:   16
pool already has pages added
******************
* After
******************
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
HugePages_Total:      16
HugePages_Free:       15
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:     524288 kB
root@ubuntu:/home/fsmith/depot/dpdk_PassCoreEvnetMsg# ./dpdk_cfg


********
* Network
********

dpdk-devbind.py -u  0002:04:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py --bind=vfio-pci  0002:04:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py -u  0002:05:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py --bind=vfio-pci  0002:05:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12


********
* event dev  (SSO)
********

dpdk-devbind.py -u  0002:0e:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py --bind=vfio-pci  0002:0e:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12


********
* mempool  (fpa)
********

dpdk-devbind.py -u  0002:0d:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py --bind=vfio-pci  0002:0d:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12


********
* crypto
********

dpdk-devbind.py -u  0002:10:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
dpdk-devbind.py --bind=vfio-pci  0002:10:00.0
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
lspci: Unable to load libkmod resources: error -12
root@ubuntu:/home/fsmith/depot/dpdk_PassCoreEvnetMsg#
root@ubuntu:/home/fsmith/depot/dpdk_PassCoreEvnetMsg# ./x
EAL: Detected 24 lcore(s)
EAL: Detected 1 NUMA nodes
EAL: Multi-process socket /var/run/dpdk/rte/mp_socket
EAL: Selected IOVA mode 'VA'
EAL: No available hugepages reported in hugepages-2048kB
EAL: Probing VFIO support...
EAL: VFIO support initialized
EAL: PCI device 0002:0e:00.0 on NUMA socket 0
EAL:   probe driver: 177d:a0f9 event_octeontx2
EAL:   using IOMMU type 1 (Type 1)
[14504.043141] vfio_cap_init: 0002:0e:00.0 hiding cap 0x14
 -t option= 5
 rte_log_get_stream:  0xaaaab3218130
 STDOUT:  0xffffab9c5560
 rte_gloable_log_level: 8
 rte_log_get_level(RTE_LOGTYPE_MBUF) : 7
 rte_log_get_level(RTE_LOGTYPE_MEMPOOL) : 8
 rte_log_get_level(RTE_LOGTYPE_MBUF)    : 8
 rte_log_get_level(RTE_LOGTYPE_USER1)   : 8
 rte_log_get_level(RTE_LOGTYPE_EVENTDEV): 7
 rte_log_cur_msg_loglevel per_lcore? 6
 rte_log_cur_msg_logtype  per_lcore? 0
Set "pmd.net.octeontx.init"  to RTE_LOG_DEBUG
  Id assigned to  "pmd.net.octeontx.init"  : 145
  Verify "pmd.net.octeontx.init" Level: 8
MEMPOOL:  Log Message Test to MEMPOOL fs_test
USER1:  Log Message Test to USER1: fs_test
 found 1 event devices
 g_evt_dev_id:  0
 nb_lcores:      4
 g_nb_PortsPerCore:   1
 g_nb_QueuesPerCore:  1
 result    0
 default: dev_info
      struct rte_event_dev_info:
          char *        driver_name  event_octeontx2
          rte_device * dev          0xaaaab3595970
          uint32_t     min_dequeue_timeout_ns           0x000003e8
          uint32_t     max_dequeue_timeout_ns           0x000f9c18
          uint32_t     dequeue_timeout_ns               0x00000000
          uint8_t      max_event_queues                 0x10
          uint32_t     max_event_queue_flows            0x00100000
          uint8_t      max_event_queue_priority_levels  0x08
          uint8_t      max_event_priority_levels        0x01
          uint8_t      max_event_ports                  0x0d
          uint8_t      max_event_port_dequeue_depth     0x01
          uint32_t     max_event_port_enqueue_depth     0x00000001
          int32_t      max_num_events                   0xffffffff
          uint32_t     event_dev_cap                    0x000001cd
 config values for event_dev_config
      struct rte_event_dev_config:
          uint32_t dequeue_timeout_ns             0x00000000
          int32_t  nb_events_limit     x          0xffffffff
          uint8_t  nb_event_queues     x          0x04
          uint8_t  nb_event_ports                 0x04
          uint32_t nb_event_queue_flows     x     0x00000400
          uint32_t nb_event_port_dequeue_depth  x 0x00000001
          uint32_t nb_event_port_enqueue_depth  x 0x00000001
          uint32_t event_dev_cfg                  0x00000000
default: event dev -> queue info event_q_id=0
      struct rte_event_queue_conf *
          uint32_t nb_atomic_flows              0x00100000
          uint32_t nb_atomic_order_sequences    0x00100000
          uint32_t event_queue_cfg              0x00000001
          uint8_t  schedule_type                0x00 (RTE_SCHED_TYPE_ORDERED)
          uint8_t  priority                     0x80
 Config: event queue config  -> event_q_conf
      struct rte_event_queue_conf *
          uint32_t nb_atomic_flows              0x00000400
          uint32_t nb_atomic_order_sequences    0x00000400
          uint32_t event_queue_cfg              0x00000001
          uint8_t  schedule_type                0x01 (RTE_SCHED_TYPE_ATOMIC)
          uint8_t  priority                     0x80
 default: event port config  -> def_p_conf
      struct rte_event_port_conf *
          int32_t  new_event_threshold        0xffffffff
          uint16_t dequeue_depth              0x0001
          uint16_t enqueue_depth              0x0001
          uint8_t  disable_implicit_release   0x00
 Config: event port config  -> event_p_conf
      struct rte_event_port_conf *
          int32_t  new_event_threshold        0xffffffff
          uint16_t dequeue_depth              0x0001
          uint16_t enqueue_depth              0x0001
          uint8_t  disable_implicit_release   0x00
 Linking Port 0 to queue 0
 Linking Port 1 to queue 1
 Linking Port 2 to queue 2
 Linking Port 3 to queue 3
 service ID for event_dev (Device?) 0
 Event Dev Device Started successfully
 Launch code on Core: 1
 Launch code on Core: 2
 Launch code on Core: 3
*** Put an event into the Event Dev Scheduler ***
        dev_id: 0
         port_id: 0
         lcore_id: 0
   ev        add:0xffffc83338d0
     flow_id        57005
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       1
     priority       128
     event_ptr      0xaaaab13d5120
 Launch code on Core: 0
****    c1) Received 1 events ****
  event[0]        add:0xffffaa84cf50
     flow_id        57005
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       1
     priority       0
     event_ptr      0xaaaab13d5120
 Message:   Eat
  c1) Send message to next Port (aka core for me)  2 events
****    c2) Received 1 events ****
  event[0]        add:0xffffaa03cf50
     flow_id        57006
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       2
     priority       0
     event_ptr      0xaaaab13d5118
 Message:   At
  c2) Send message to next Port (aka core for me)  3 events
****    c3) Received 1 events ****
  event[0]        add:0xffffa982cf50
     flow_id        57007
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       3
     priority       0
     event_ptr      0xaaaab13d5110
 Message:   Joes
  c3) Send message to next Port (aka core for me)  0 events
****    c0) Received 1 events ****
  event[0]        add:0xffffc83338e0
     flow_id        57008
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       0
     priority       0
     event_ptr      0xaaaab13d5100
 Message:   Bar & Grill
  c0) Send message to next Port (aka core for me)  1 events
****    c1) Received 1 events ****
  event[0]        add:0xffffaa84cf50
     flow_id        57009
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       1
     priority       0
     event_ptr      0xaaaab13d5120
 Message:   Eat
  c1) Send message to next Port (aka core for me)  2 events
****    c2) Received 1 events ****
  event[0]        add:0xffffaa03cf50
     flow_id        57010
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       2
     priority       0
     event_ptr      0xaaaab13d5118
 Message:   At
  c2) Send message to next Port (aka core for me)  3 events
****    c3) Received 1 events ****
  event[0]        add:0xffffa982cf50
     flow_id        57011
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       3
     priority       0
     event_ptr      0xaaaab13d5110
 Message:   Joes
  c3) Send message to next Port (aka core for me)  0 events
****    c0) Received 1 events ****
  event[0]        add:0xffffc83338e0
     flow_id        57012
     sub_event_type 3
     event_type     3
     op             0
     sched_type     2
     queue_id       0
     priority       0
     event_ptr      0xaaaab13d5100
 Message:   Bar & Grill
  c0) Send message to next Port (aka core for me)  1 events
^C

Signal 2 received, preparing to exit...
[otx2_sso_dump] SSO running in [dual_ws] mode
[otx2_sso_dump] SSO dual workslot[0] vws[0] dump
SSOW_LF_GWS Base addr   0x2180800000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump]SSO dual workslot[0] vws[1] dump
SSOW_LF_GWS Base addr   0x2180801000
SSOW_LF_GWS_LINKS       0x430000c0000000
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0xa3030deb4
SSOW_LF_GWS_WQP         0xa3030deb4
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump] SSO dual workslot[1] vws[0] dump
SSOW_LF_GWS Base addr   0x2180802000
SSOW_LF_GWS_LINKS       0x440001c0000000
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x1a3030deb5
SSOW_LF_GWS_WQP         0x1a3030deb5
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump]SSO dual workslot[1] vws[1] dump
SSOW_LF_GWS Base addr   0x2180803000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump] SSO dual workslot[2] vws[0] dump
SSOW_LF_GWS Base addr   0x2180804000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump]SSO dual workslot[2] vws[1] dump
SSOW_LF_GWS Base addr   0x2180805000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump] SSO dual workslot[3] vws[0] dump
SSOW_LF_GWS Base addr   0x2180806000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump]SSO dual workslot[3] vws[1] dump
SSOW_LF_GWS Base addr   0x2180807000
SSOW_LF_GWS_LINKS       0x0
SSOW_LF_GWS_PENDWQP     0x0
SSOW_LF_GWS_PENDSTATE   0x0
SSOW_LF_GWS_NW_TIM      0x0
SSOW_LF_GWS_TAG         0x300000000
SSOW_LF_GWS_WQP         0x300000000
SSOW_LF_GWS_SWTP        0x0
SSOW_LF_GWS_PENDTAG     0x0
[otx2_sso_dump]SSO group[0] dump
SSO_LF_GGRP Base addr   0x2180700000
SSO_LF_GGRP_QCTL        0x1
SSO_LF_GGRP_XAQ_CNT     0x0
SSO_LF_GGRP_INT_THR     0x0
SSO_LF_GGRP_INT_CNT     0x0
SSO_LF_GGRP_AQ_CNT      0x0
SSO_LF_GGRP_AQ_THR      0x1FFFFFFFF
SSO_LF_GGRP_MISC_CNT    0x0
[otx2_sso_dump]SSO group[1] dump
SSO_LF_GGRP Base addr   0x2180701000
SSO_LF_GGRP_QCTL        0x1
SSO_LF_GGRP_XAQ_CNT     0x0
SSO_LF_GGRP_INT_THR     0x0
SSO_LF_GGRP_INT_CNT     0x0
SSO_LF_GGRP_AQ_CNT      0x0
SSO_LF_GGRP_AQ_THR      0x1FFFFFFFF
SSO_LF_GGRP_MISC_CNT    0x0
[otx2_sso_dump]SSO group[2] dump
SSO_LF_GGRP Base addr   0x2180702000
SSO_LF_GGRP_QCTL        0x1
SSO_LF_GGRP_XAQ_CNT     0x0
SSO_LF_GGRP_INT_THR     0x0
SSO_LF_GGRP_INT_CNT     0x0
SSO_LF_GGRP_AQ_CNT      0x0
SSO_LF_GGRP_AQ_THR      0x1FFFFFFFF
SSO_LF_GGRP_MISC_CNT    0x0
[otx2_sso_dump]SSO group[3] dump
SSO_LF_GGRP Base addr   0x2180703000
SSO_LF_GGRP_QCTL        0x1
SSO_LF_GGRP_XAQ_CNT     0x0
SSO_LF_GGRP_INT_THR     0x0
SSO_LF_GGRP_INT_CNT     0x0
SSO_LF_GGRP_AQ_CNT      0x0
SSO_LF_GGRP_AQ_THR      0x1FFFFFFFF
SSO_LF_GGRP_MISC_CNT    0x0
************
    stopping the event_dev device  sso
***************
************
    closing the event_dev device  sso
***************
  thank you very much
Bye...
root@ubuntu:/home/fsmith/depot/dpdk_PassCoreEvnetMsg#

