# MD5
**MD5**（Message-Digest-Algorithm 5）：稱摘要演算法或哈希演算法，演算法的雜湊大小是 128 位元。前身是MD2、MD3、MD4，是廣泛使用的雜湊演算法之一。原理是輸入不定長度信息，輸出固定長度128-bits的演算法。經過程序流程，生成四個32位數據，最後聯合起來成為一個128-bits _from WIKI_

>使用方式：
>1. using namespace：_System.Security.Cryptography_ 
>2. 實作 MD5CryptoServiceProvider 類別的 ComputeHash 計算雜湊值

````CS
  
/// <summary>
/// 16位：ComputeHash
/// </summary>
/// <param name="input"></param>
/// <returns></returns>
private string getMd5Method(string input)
{
	MD5CryptoServiceProvider md5Hasher = new MD5CryptoServiceProvider();
	byte[] myData = md5Hasher.ComputeHash(Encoding.Default.GetBytes(input));
	StringBuilder sBuilder = new StringBuilder();

	for (int i = 0; i < myData.Length; i++)
	{
		sBuilder.Append(myData[i].ToString("x"));
	}

	return string.Format("ComputeHash(16)：{0}", sBuilder.ToString());
}

````

````cs
/// <summary>
/// 32位加密：ComputeHash
/// </summary>
/// <param name="input"></param>
/// <returns><</returns>
private string getMd5Method2(string input)
{
	MD5CryptoServiceProvider md5Hasher = new MD5CryptoServiceProvider();
	byte[] myData = md5Hasher.ComputeHash(Encoding.Default.GetBytes(input));
	StringBuilder sBuilder = new StringBuilder();

	for (int i = 0; i < myData.Length; i++)
	{
		sBuilder.Append(myData[i].ToString("x2"));
	}

	return string.Format("ComputeHash(32)：{0}", sBuilder.ToString());

}

````

````cs
/// <summary>
/// 32位加密：直接使用HashPasswordForStoringInConfigFile
/// </summary>
/// <param name="input"></param>
/// <returns><</returns>
private string getMd5Method3(string input)
{
	string myReturn = System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(input, "MD5");
	return string.Format("HashPasswordForStoringInConfigFile(32)：{0}", myReturn.ToString());

}

````

---
# SHA1
**SHA1**（Secure Hash Algorithm）：安全雜湊演算法，演算法的雜湊大小是 160 位元。由[美國國家安全局](http://zh.wikipedia.org/wiki/%E7%BE%8E%E5%9C%8B%E5%9C%8B%E5%AE%B6%E5%AE%89%E5%85%A8%E5%B1%80)(NSA)設計，為SHA家族的其中一種演算法（SHA-1、SHA-224、SHA-256、SHA-384，和SHA-512）後四者有時並稱為SHA-2，SHA-1在許多安全協定中廣為使用（_註三_）。是一種能計算出一個數位訊息所對應到的，長度固定的字串（又稱訊息摘要）的演算法。_from [WIKI SHA家族](http://zh.wikipedia.org/wiki/SHA_%E5%AE%B6%E6%97%8F)_

使用方式：
>1. using namespace：_System.Security.Cryptography_
>2. 實作 SHA1CryptoServiceProvider 類別的 ComputeHash 計算雜湊值
程式寫法與MD5大致相同，直接show加密後結果