The following are the buffer pool functions

initBufferPool:
This function is utilized to initialize the Buffer Manager. It accepts paramters like Buffer Manager handle, Page File Name, No. of Pages and Replacement Strategy. 
It first checks if Page File referred by Page File Name is existing or not. If the Page File doesn't exist then it returns error (RC_FILE_NOT_FOUND) to the calling function.


If the Page File exisits then variables from Buffer Manager structure are initialized with appropriate values.
 It also initializes a two dimensional array of size (numPages)(6). 
After initilizing the array, we are assigning it to Management Data (mgmtData) pointer. Finally, function returns success (RC_OK) to the calling function.


shutdownBufferPool:
This function is used to shutdown the Buffer Manager.

First, it calls forceFlushPool function to write all the dirty pages to disk. 
Next, it iterates through all the Buffer Manager pages, adds up the fix count for all the pages. If the final fix count is greater than zero then Buffer Manager will not be shutdown else the memory for all the pages and and finally the handle will be freed. 
Finally, function returns sucess (RC_OK) or error (RC_ERROR_BUFFER) to the calling function.




forceFlushPool:
This function is used to write all the dirty pages from Buffer Manager to disk.

It iterates through all the Buffer Manager pages, checks if it is dirty. 
If the page is dirty then the page is written back to disk using "forcePage" function. If the forcePage function returns the success then the dirty bit corresponding to the page will be set to clean in the Management Data array. 
Finally, function returns sucess (RC_OK) or error (RC_ERROR_BUFFER) to the calling function.





The following are the Page Management functions:

markDirty : the markDirty function accepts the variables of BM_BufferPool type and BM_PageHandle type. It iterates through all the buffer manager pages and checks if the buffer manager is not empty, checks if the pageNum is one of the bufferpool , if the page is found we will update the dirty flag to 1. If the page is not found, the dirty flag is not changed. If the flag is set successfully, then we return RC_OK flag, if not we would return the error message saying the page is not found else we have to return that the buffer is empty if the entire buffer is empty.

unpinPage: This function iterates through the buffer manager and checks for the requested pageNum from the BM_PageHandle. We will iterate through the buffer pool and check If the requested pageNum is found in the buffer pool, then we will set the flag to unpin the page and return RC_OK. If the pageNum is not found, we will return an error of RC_PAGEFRAME_NOTFOUND. If the buffer pool itself is empty, then we will return RC_BUFFER_EMPTY error.

forcePage : On calling this forcePage function, the buffer manager should write all the data in the buffer pool into the disk regardless if that particular data is changed or if its the same page as in the disk. On successful write,we return RC_OK else we will return that the write failed. For writing the data into the disk, we will reuse the functionality that was written for storage_mgr.

pinPage: The data stored in the pageNum is the page number that is marked to pin the page, i.e. we will load the data of the page that has pageNum in the disk. If the file doesnt have that pageNum , then we ensure the capacity by the increasing the file size and then loading that pageNum into the PageHandle variable data. If the pageNum data is already present in the buffer pool, then we just point the pointer data to that particular page and return RC_OK. Along with pointing the data, we will also update the other details like pageNum , pinCount.



THe following are Statistic information functions:

getFrameContents : This function returns an integer array, where we will return the pageNumber's of the buffer manager details.

getDirtyFlags: This function returns an array of bool, where for each pageNum in the buffer pool we check if the dirty flag is 1, if the flag is set to 1 , we make that entry as TRUE. If the dirty flag is set to 0, then we make that entry as FALSE. Once the details of all the pages are obtained, we return an array of boolean values.

getFixCounts : This function returns an array of fixCounts for each of the pageNum in the buffer pool by iterating over the buffer pool. Once all the page's data is collected, we will return the array of these integer values.

getNumReadIO : This function returns the number of read IO's that was done i.e. reading the data from disk into the buffer. The counter starts from the time that buffer pool was intialized to store all the data.

getNumWriteIO: This function returns the number of write IO's that was done i.e., writing the block of data onto the disk from the buffer pool. The counter for WriteIO too starts from the time the buffer pool was intialized to store the data.