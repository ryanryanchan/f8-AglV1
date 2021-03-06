# f8-AglV1
f8 resource management algorithm

working on an algorithm to choose which models to download for the system application.
Given a project ID, latitude, longitude and a list of current models in the machine (gpu, cpu, cache):
decide what data needs to be moved/deleted from within the machine, and what data needs to be downloaded from the cloud.
##### final output: (vector\<int\> model ID's)
- cpu_to_gpu[]
- gpu_delete[]
- cloud_to_cpu[]
- cache_to_cpu[]
- cpu_delete[]
- cpu_to_cache[]
- cache_delete[]

### algorithm logic:
1. build startlist
   - parse all instances into list of structs
2. create modelList
   - get list of models from instances, calculate full score of each model
3. loop through modelList:
   - if in gpu, leave it
   - else put items in cpu_to_gpu
   - else put items in cache_to_cpu
   - else put remaining items in cloud_to_cpu
4. GPU_SORT:
   - merge lists cpu_to_gpu and gpu_delete sorted by score until gpu is full.
     - **gpu_delete starts as a full gpu_data list, we delete from it as we find things that we want to keep.
   - delete leftover data
5. CPU_SORT
   - if in cpu_to_gpu, keep
   - if not cached, keep
   - at this point, if current cpu memory is over threshold, cache everything it can.
   - merge cache_to_cpu, cloud_to_cpu, cpu_data sorted by score until we reach capcity
     - **cpu_delete starts as cpu_data, we delete from it when find we want to keep an item.
   - cpu_delete is now a list of everything not needed:
     - already in gpu
     - irrelevant to current request
   - if cpu memory is under threshold:
     - don't delete anything
6. CACHE_SORT:
   - if in cache_to_cpu, keep
   - add everything from cpu_to_cache
   - if there's room, add things that were in cache_data
   - delete remainder
  
### model sorting:
Items are sorted by how much potential screenspace they could take up. 
The closer they are, the more space they take. The more of the same model there is around you, the more space they take up.
One individual instance would take up some degree angle of a 360 degree view. 
One model's score would be the total collection of every individual score of every instance.
Because the actual angle is found with arctan, but this is applied to every instance, we can ignore trigonometry. 
- sum( size / distance)

### work in progress:
- [ ] figure out model size
- [ ] figure out model bytesize
- [ ] prepare for latitude, longitude, and altitude.
