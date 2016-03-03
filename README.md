

首先，看一下C/C++中的路径解析与合成的两个函数`_splitpath`与`_makepath`：

```c
_CRT_INSECURE_DEPRECATE(_splitpath_s) _CRTIMP void   __cdecl _splitpath(_In_z_ const char * _FullPath, _Pre_maybenull_ _Post_z_ char * _Drive, _Pre_maybenull_ _Post_z_ char * _Dir, _Pre_maybenull_ _Post_z_ char * _Filename, _Pre_maybenull_ _Post_z_ char * _Ext);
  
void  __cdecl _makepath(char* _Path, const char* _Drive, const char* _Dir, const char* _Filename, const char* _Ext);
```

不要被复杂的函数声明给吓到，其实很简单。

对于`_splitpath`路径解析函数：

 - `_FullPath`： 全路径(`input`)
 - `_Drive` ： 盘符(`output`)
 - `_Dir`： 除去盘符和文件名的中间路径(`output`)
 - `_Filename`：文件名(`output`)
 - `_Ext`：拓展名(`output`)

对于`_makepath`路径合成函数，上述的参数含义相同，只是输入与输出相反。

给出一段代码示例：

```c
#include <stdlib.h>  
#include <stdio.h>  
  
void main( void )  
{  
   char full_path[_MAX_PATH];  
   char drive[_MAX_DRIVE];  
   char dir[_MAX_DIR];  
   char fname[_MAX_FNAME];  
   char ext[_MAX_EXT];  
  
   _makepath( full_path, "c", "\\sample\\file\\", "makepath", "c" );  
   printf( "_FullPath created with _makepath: %s\n\n", full_path);  
   _splitpath( full_path, drive, dir, fname, ext );  
   printf( "Path extracted with _splitpath:\n" );  
   printf( "  _Drive: %s\n", drive );  
   printf( "  _Dir: %s\n", dir );  
   printf( "  _Filename: %s\n", fname );  
   printf( "  _Ext: %s\n", ext );  
}  
  
  
// Output  
  
_FullPath created with _makepath: c:\sample\file\makepath.c

Path extracted with _splitpath:
  _Drive: c:
  _Dir: \sample\file\
  _Filename: makepath
  _Ext: .c
```

其中的一些宏定义如下：
```c
/*
 * Sizes for buffers used by the _makepath() and _splitpath() functions.
 * note that the sizes include space for 0-terminator
 */
#define _MAX_PATH   260 /* max. length of full pathname */
#define _MAX_DRIVE  3   /* max. length of drive component */
#define _MAX_DIR    256 /* max. length of path component */
#define _MAX_FNAME  256 /* max. length of file name component */
#define _MAX_EXT    256 /* max. length of extension component */
```

有时候，我们仅需要获得文件的文件名或者拓展名，那么用上述的方法就有点繁琐，`string`类型及其操作函数就能很方便地实现：

```c
string filePath = "E:\\file\\main.cpp";

string extendName;
int iBeginIndex  = filePath.find_last_of(".")+1;
int iEndIndex    = filePath.length();
extendName       = filePath.substr( iBeginIndex, iEndIndex-iBeginIndex );
transform( extendName.begin(), extendName.end(), extendName.begin(), tolower ); 
cout << extendName << endl;

// Output : cpp
```

同理，如果想用该方法获取文件名，只需将`filePath.find_last_of(".")`修改为`filePath.find_last_of("\\")`，另外，这里为了使输出的文件拓展名一致，使用了`transform`函数，其中的参数`tolower`，含义是如果该字符有对应的小写字符，则转为小写字符。
