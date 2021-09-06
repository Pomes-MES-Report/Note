# MemoryStream和BufferedStream

## 讀寫記憶體-MemoryStream類

MemoryStream類用於向記憶體而不是磁碟讀寫資料。MemoryStream封裝以**無符號位元組陣列**形式儲存的資料，該陣列在建立MemoryStream物件時被初始化，或者該陣列可建立為空陣列。可在記憶體中直接訪問這些封裝的資料。記憶體流可降低應用程式中對臨時緩衝區和臨時檔案的需要。

下表列出了MemoryStream類的重要方法：
>1、Read()：讀取MemoryStream流物件，將值寫入快取區。
>2、ReadByte()：從MemoryStream流中讀取一個位元組。
>3、Write()：將值從快取區寫入MemoryStream流物件。
>4、WriteByte()：從快取區寫入MemoytStream流物件一個位元組。

Read方法使用的語法如下：
>```csharp
mmstream.Read(byte[] buffer,offset,count) 

其中mmstream為MemoryStream類的一個流物件，3個引數中，buffer包含指定的位元組陣列，該陣列中，從offset到(offset count-1)之間的值由當前流中讀取的字元替換。Offset是指Buffer中的位元組偏移量，從此處開始讀取。Count是指最多讀取的位元組數。Write()方法和Read()方法具有相同的引數型別。

MemoryStream類的使用例項：
````CS
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;	
using System.IO;

namespace ConsoleApplication1 {
	class Program{
		static void Main(){
			int count; 
			byte[] byteArray;
			char[] charArray;
			UnicodeEncoding uniEncoding = new UnicodeEncoding();
			// Create the data to write to the stream. 
			byte[] firstString =uniEncoding.GetBytes("一二三四五");
			byte[] secondString = uniEncoding.GetBytes("上山打老虎");
			
			using (MemoryStream memStream = new MemoryStream(100)) {
				// Write the first string to the stream.
				memStream.Write(firstString, 0, firstString.Length);
				// Write the second string to the stream, byte by byte.
				count = 0;
				while (count < secondString.Length){
					memStream.WriteByte(secondString[count ]);
				}
				// Write the stream properties to the console.
				Console.WriteLine("Capacity={0},Length={1},Position={2}\n",
					memStream.Capacity.ToString(), memStream.Length.ToString(),
					memStream.Position.ToString());
				
				// Set the position to the beginning of the stream. 	  		
				memStream.Seek(0, SeekOrigin.Begin);
				
				// Read the first 20 bytes from the stream.
				byteArray = new byte[memStream.Length];
				count = memStream.Read(byteArray, 0, 20);
				// Read the remaining bytes, byte by byte.
				while (count < memStream.Length) {
					byteArray[count ] = Convert.ToByte(memStream.ReadByte());
				}
				
				// Decode the byte array into a char array
				// and write it to the console.
				charArray = new char[uniEncoding.GetCharCount(byteArray, 0, count)];
				uniEncoding.GetDecoder().GetChars(byteArray, 0, count, charArray, 0);
				Console.WriteLine(charArray); Console.ReadKey();
			}
			
		}
	}
}



````

## 讀取記憶體流中的資料
在.NET中，使用抽象基類System.IO.Stream代表流，它提供Read和Write兩個方法。由於資料流的有序性，因此流物件還有一個讀寫指標，為此，Stream類還有一個Seek方法用於移動讀寫指標。  

字串與位元組陣列間的互相轉化：

````CS
string str = "記憶體大小";
byte[] temp = Encoding.UTF8.GetBytes (str); // 字串轉化為位元組陣列 
string s = Encoding.UTF8.GetString (temp); // 位元組陣列轉化為字串
Debug.Log (s);
````

Encoding用法比較簡單，如果只是位元組和字元的互相轉換，GetBytes()和GetChars()這兩個方法及它們的過載基本上會滿足你所有要求。

GetByteCount()及其過載是得到一個字串轉換成位元組時實際的位元組個數。

GetCharCount()及其過載是得到一個位元組陣列轉換成字串的大小。

---

# Decoder.GetChars 方法

[Decoder.GetChars  (Byte[], Int32, Int32, Char[], Int32)](https://msdn.microsoft.com/zh-cn/library/125z2etb(v=vs.80).aspx)

在派生類中重寫時，將指定位元組陣列的位元組序列和內部緩衝區中的任何位元組解碼到指定的字元陣列。

在派生類中重寫時，將一個位元組序列解碼為一組字元。

Java裡一個byte取值範圍是-128~127, 而C#裡一個byte是0~255.

首位不同. 但是底層I/O儲存的資料是一樣的

FileStream物件的資料來自檔案，而MemoryStream物件的資料來自記憶體緩衝區。這兩個類都繼承自Stream類。 

MemoryStream的資料來自記憶體中的一塊連續區域，這塊區域稱為“緩衝區(Buffer)”。可以把緩衝區看成一個陣列，每個陣列元素可以存放一個位元組的資料。

````CS
//位元組陣列
byte[] buffer = new byte[600];
//填充位元組陣列
private void CreateExampleData() {
	for(int i=0; i<600; i ){
		//byte型別的數最大不能超過255，用256取模實現
		buffer[i] = (byte)(i%256);
		
	}

}

//記憶體流的基本使用方法：

private void OnTestMemory() {
	//建立測試資料
	CreateExampleData();
	
	//建立記憶體流物件，初始分配50位元組的緩衝區
	MemoryStream mem = new MemoryStream(50);
	
	//向記憶體流中寫入位元組陣列的所有資料
	mem.Write(buffer,0,buffer.GetLength(0));
	
	MessageBox.Show("寫入資料後的記憶體流長度：" mem.Length.ToString());
	MessageBox.Show("分配給記憶體流的緩衝區大小：" mem.Capacity.ToString());
	mem.SetLength(550);
	MessageBox.Show("呼叫SetLength方法後的記憶體流長度：" mem.Length.ToString());
	mem.Capacity = 620;//此值不能小於Length屬性
	MessageBox.Show("呼叫Capacity方法後緩衝區大小：" mem.Capacity.ToString());
	
	//將讀寫指標移到距流開頭10個位元組的位置
	mem.Seek(10,SeekOrigin.Begin);
	MessageBox.Show(mem.ReadByte().ToString());
	mem.Close();
}

````

記憶體流的Length屬性代表了其中存放的資料的真實長度，而Capacity屬性則代表了分配給記憶體流的記憶體空間大小。 可以使用位元組陣列建立一個固定大小的MemoryStream，
>````CS 
MemoryStream mem = new MemoryStream(buffer);

這時，無法再設定Capacity屬性的大小。 還可以建立只讀的記憶體流物件。
>````CS 
>MemoryStream mem = new MemoryStream(buffer,false);

FlieStream用於存取檔案。建立檔案並寫入內容：
````CS
FileStream fsForWrite = new FileStream("test.data",FileMode.Create);
try{
	//寫入一個位元組
	fsForWrite.WriteByte(100);
	CreateExampleData();
	
	//將位元組陣列寫入檔案
	fsForWrite.Write(buffer,0,buffer.GetLength(0));
}catch(Exception ex){
	MessageBox.Show(ex.Message);
}
finally{
	fsForWrite.Close();
}


````