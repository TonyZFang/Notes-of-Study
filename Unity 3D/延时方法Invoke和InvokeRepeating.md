Unity3D:延时方法Invoke和InvokeRepeating

MonoBehaviour里面有两个内置的延时方法

Invoke
Invoke(methodName: string, time: float): void;
methodName:方法名
time:多少秒后执行

InvokeRepeating
InvokeRepeating(methodName: string, time: float, repeatRate: float): void;
methodName:方法名
time:多少秒后执行
repeatRate：重复执行间隔

还有两个重要的方法：
IsInvoking：用来判断某方法是否被延时，即将执行
CancelInvoke：取消该脚本上的所有延时方法
可以使用IsInvoking(方法名)和CancelInvoke(方法名)来判断某方法是否挂起或取消挂起某方法。如果这两个方法没有传入任何参数，则会判断当前对象下是否有挂起的方法或取消所有挂起的方法。

下面是代码笔记:
using UnityEngine;
using System.Collections;

public class DelayScript : MonoBehaviour {
    //当前时间
    private float nowTime;
    //执行重复方法的次数
    private int count;
    // Use this for initialization
    void Start () {
        nowTime = Time.time;
        Debug.Log("时间点："+nowTime);
        this.Invoke("setTimeOut", 3.0f);
        this.InvokeRepeating("setInterval", 2.0f, 1.0f);
    }

    private void setTimeOut()
    {
        nowTime = Time.time;
        Debug.Log("执行延时方法：" + nowTime);
    }

    private void setInterval()
    {
        nowTime = Time.time;
        Debug.Log("执行重复方法：" + nowTime);
        count += 1;
        if(count==5)
            this.CancelInvoke();
    }
}

原文链接：https://blog.csdn.net/azhou_hui/article/details/47028029
