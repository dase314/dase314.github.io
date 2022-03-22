
######  Should be aware of the storage hierarchy
  
  1. Database is usually data-intensive
  2. cache miss is important

######  Pay attention to the hardware properties
  
  1. Sequential access and random access is usually different
  2. bandwith and IOPS


###### Design Issues

   1. choose a proper index
   2. support variable length data
   3. for most secondary storage, try avoid random writes
   4. reduce the write amplication
   5. group your writes 

###### Optimization On LSM-tree

   
   1. optimize write amplication
   2. optimize read performance
   3. other optimizations: cache, special workload



