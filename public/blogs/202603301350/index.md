# E10_Volume Basics 
create a sphere and note isooffset.  
isooffset will create a volume box around sphere,in shpere density is 1 and out of sphere is 0.  
next use volumewrangle note VEXpression.  
``` 
float d = f@density;  

d *= noise(@P * chf("mult"));  

f@float d = f@density;  

d *= noise(@P * chf("mult"));  

f@density = d;  
```
![noise density](./Screen1.webp)  
  
  
接下来是function volumesample：该函数可以采样volume primitives 的pos位置的float value  
```c  
//先提取点的pos  
vector pos = point(1, "P", 0);  
//计算pos的distance相对位置的值  
float dist = distance(@P, pos);  

//采样pos位置的density并赋值给volume 的density  
float d = volumesample(0, "density",pos);  
  
//让pos的distance小于density * 0.5的范围内赋值d给density，不再此范围内的denstiy全部为0  
if(dist < d*0.5)  
{  
  f@density = d;  
}else{  
      f@density = 0;  
}  
```  
![volumesample](./VolumeSample.webp)  
  

