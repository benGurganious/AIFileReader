# AIFileReader
An intelligent file reader in c++ that uses a lightweight interpreter to understand file specifications.

Please ask permission to use this code. It is a work-in-progress and some discussion must happen for you to understand what you are using. For larger, more professional projects: contact me for a commercial-grade, professional version. This makes file reading in c++ a breeze. Create your own file formats or set this to read existing formats. Your own formats are easily created to be solid and error-proof following the design here. All rights reserved for the design, algorithms, and source-code. Special permission required to use. Better versions exist and can be created for any purpose.

File specifications must be in the proper format.

Usage:

1) Create a file specification for the file you want to read.
2) Create a class/struct to hold values from the file.
2) Call the Interpreter to read your file specification and build a file reader.
  - this part is automatic
  - the returned file reader object will only be able to read files of that particular specification
3) Create a function to iteratively call the file reader object. You are responsible for the size of the buffer used to read the file.
  - the file reader object will read each value into your buffer, until the buffer is full.
  - a delimiter, specified by you, is appended to the end of every data-section/property in the file.
4) Point the file reader to a file and begin reading using the function created in step 3.
  - readNext() will fill the buffer with as much data as possible without breaking sections.
  - if a section will not fit in the buffer: the buffer will not fill completely and the next call to readNext() will place as much data as possible into the buffer, without a delimiter on the end unless the end is reached. Example(500 byte buffer): 400 bytes have been read into the buffer and the next section is 600 more bytes. The reader will only fill the buffer with 400 bytes and the next call will place 500 bytes into the buffer, null terminated. The last call will place the remaining 100, followed by a delimiter, and any other section that will fit into the remaining 399 bytes.

Benefits over creating custom readers for every file:
  -Shorthand file specifications are quick/easy to make.
  -Variable length sections in files can be difficult for unskilled programmers
  -Your parsing code will be easier to debug as the AI won't break unless there is a problem with the file or the file specification

Overall, using this program saves time. All you have to do is create your own object to hold the values and a file specification. If you are reading a file: you probably need to make your own objects anyways. At least now you don't also have to make a file reader.

Example psuedocode:
  File Specification:
    <2:0><(0)><2:1><(1)*10>  
      <2:0> means a 2 byte value is set to variable[0] and not necessary in your object, only necessary for the file reader
      <(0)> means to read variable[0] amount of bytes
      <2:1> same as <2:0> but set to variable[1]
      <(1)*10> means to read 10 items of size variable[1] bytes
      
      A file of the above specification may look like (file is expected to be a binary file; ASCII files can be read with correct specification creation although likely unecessary to use this reader for such purpose):
        0x00FF <0xff bytes here> 0x0F00 <10 items of 0x0F00 bytes or 0x0F00 items of 10 bytes>
      
  Using the reader:
    MyObj obj = new MyObj()
    AbstractFileReader reader = new AbstractFileReader(interpret(specification),filename_to_read)
    char* buff = new char[501] //use +1 for a null term on the end
    char delim = '|'
    int nbytes = 0
    int i =0
    while(nbytes=reader.readNext(buff,500,delim) >0) //null terminator will be set to 501 if buffer is completely filled
      i=setProperties(i,obj,buff,nbytes,delim) //a null terminator marks the end of the data
                            //everything before the desired delimiter, in this example '|', is the property/data of the section
                            


Note:
This reader will attempt to fill the buffer on each call to readNext. For best performance: set the buffer as large as is practical. If done: this reader will be as fast as can be possible for just about any purpose. Using this means there will never be a need to create a special file reader ever again.

Future changes:
-entire buffer amount will be read and data parsed after-the-fact by the reader
-custom object will be able to be created by the reader, making it possible to simply read 1000 files without hardly any coding
-custom formatting for file specifications. If you want it to understand <2->0> instead of <2:0> then it will be possible.

Currently the reader will read one section at a time although it is known to be faster to read the entire buffer amount then parse after. This is coming in the next change. The current version was a test of a simple interpreter and done with little planning.

At first glance this may seem to be an impractical/unnecessary thing to do. Not true. Shorthand file specifications are easy to create and require much less code. It is even shorter than using the python language. Your data is parsed and placed into a buffer organized perfectly for object creation. Very little thinking is required to make this work for just about any type of file.

Work in progress. Currently there are some limitations of the file types and system it runs on. Plan is to commit code that will work on any machine. Then, I suppose, I can make it write file readers for just about any image/data file! You may be able to as well. Ask me for permission!
