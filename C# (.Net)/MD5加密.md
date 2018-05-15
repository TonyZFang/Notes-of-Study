利用MD5CryptoServiceProvider进行MD5加密

官方文档的例子
下面的代码示例计算字符串的 MD5 哈希值，并将该哈希作为 32 字符的十六进制格式字符串返回。此代码示例中创建的哈希字符串与能创建 32 字符的十六进制格式哈希字符串的任何 MD5 哈希函数（在任何平台上）兼容。
(注意:如果密码太简单的话 会被破解，但是这个算法是个摘要算法，如果密码足够长足够复杂 就不会被解密 可以加一个适当长度的壳)
using System;
using System.Security.Cryptography;
using System.Text;

class Example
{
    // Hash an input string and return the hash as
    // a 32 character hexadecimal string.
    static string getMd5Hash(string input)
    {
        // Create a new instance of the MD5CryptoServiceProvider object.
        MD5CryptoServiceProvider md5Hasher = new MD5CryptoServiceProvider();

        // Convert the input string to a byte array and compute the hash.
        byte[] data = md5Hasher.ComputeHash(Encoding.Default.GetBytes(input));

        // Create a new Stringbuilder to collect the bytes
        // and create a string.
        StringBuilder sBuilder = new StringBuilder();

        // Loop through each byte of the hashed data
        // and format each one as a hexadecimal string.
        for (int i = 0; i < data.Length; i++)
        {
            sBuilder.Append(data[i].ToString("x2"));
        }

        // Return the hexadecimal string.
        return sBuilder.ToString();
    }

    // Verify a hash against a string.
    static bool verifyMd5Hash(string input, string hash)
    {
        // Hash the input.
        string hashOfInput = getMd5Hash(input);

        // Create a StringComparer an comare the hashes.
        StringComparer comparer = StringComparer.OrdinalIgnoreCase;

        if (0 == comparer.Compare(hashOfInput, hash))
        {
            return true;
        }
        else
        {
            return false;
        }
    }


    static void Main()
    {
        string source = "Hello World!";

        string hash = getMd5Hash(source);

        Console.WriteLine("The MD5 hash of " + source + " is: " + hash + ".");

        Console.WriteLine("Verifying the hash...");

        if (verifyMd5Hash(source, hash))
        {
            Console.WriteLine("The hashes are the same.");
        }
        else
        {
            Console.WriteLine("The hashes are not same.");
        }

    }
}
// This code example produces the following output:
//
// The MD5 hash of Hello World! is: ed076287532e86365e841e92bfc50d8c.
// Verifying the hash...
// The hashes are the same.

原文连接:https://msdn.microsoft.com/zh-cn/library/system.security.cryptography.md5cryptoserviceprovider(VS.80).aspx
