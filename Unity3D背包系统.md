# Unity3D背包系统

## 		 示例项目：InventroyTutorial

### 1.基本实现原理

**使用ObjectScript建立起背包和物品的基本信息，然后在脚本中获取他们的信息，然后生成相应的UI。**

ScriptableObject物品背包脚本：ScriptableObject文件夹

UI生成的示例脚本：BagManager.cs

物品信息的示例脚本：Item.cs  

UI显示物品信息脚本:Slot.cs

捡取物品的示例脚本：ItemOnWorld.cs



### 2.实现拖拽功能

**示例脚本：ItemOnDrag.cs**

**示例脚本：BagMove.cs**

首先要引入鼠标监听事件，设置好相应的鼠标事件，设置完毕后，基本的拖拽功能就能实现了。

```c#
//一些事件的监听API   这里使用了鼠标的监听
using UnityEngine.EventSystems;
//增加三个接口 鼠标开始拖拽 正在拖拽  和停止拖拽的事件
public class ItemOnDrag : MonoBehaviour, IBeginDragHandler, IDragHandler, IEndDragHandler
{
    //标明你再拖拽之前，你是哪一个背包格的
    public Transform originalparent;

    //这里的eventData 就是鼠标的一些数据
    public void OnBeginDrag(PointerEventData eventData)
    {
        //标明当前背包格
        originalparent = transform.parent;
        //更改物品的父级 使它一直在层级上方，不被其他UI遮挡
        //物品位置与鼠标保持一致【此方法的移动会将鼠标吸附在目标对象的中心锚点，拖动较大界面最好请使用。
        //无论拖动哪里都是移动中心描点，示例脚本：BagMove
        transform.position = eventData.position;
        //让物品处于父级的父级，也就是上升一级 这里上升两级  因为上升一级会处于Grid的势力范围，导致会被强制排版，所以要脱离Grid
        transform.SetParent(transform.parent.parent.parent);

        //让物品别挡住鼠标射线 此射线只会检测UI组件
        GetComponent<CanvasGroup>().blocksRaycasts = false;
    }

    public void OnDrag(PointerEventData eventData)
    {
        //物品位置与鼠标保持一致
        transform.position = eventData.position;
        //输出正下方的游戏对象是谁 
        Debug.Log(eventData.pointerCurrentRaycast.gameObject.name);
    }

    public void OnEndDrag(PointerEventData eventData)
    {
        
        //判断当前鼠标下面的游戏对象名是什么
        if (eventData.pointerCurrentRaycast.gameObject.name=="imgItem")
        {
            //如果拖拽的背包格有装备，自己先占住位置，然后把原本在这的装备换到之前的位置，具体方法就是设置父级，设置位置【其他的Grid会帮您办好的】
            transform.SetParent(eventData.pointerCurrentRaycast.gameObject.transform.parent.parent);
            transform.position = eventData.pointerCurrentRaycast.gameObject.transform.parent.parent.position;
            //把原本在这的装备推到我之前的位置  先设置好它父级的位置，然后设置它的父级
            eventData.pointerCurrentRaycast.gameObject.transform.parent.position = originalparent.position;
            eventData.pointerCurrentRaycast.gameObject.transform.parent.SetParent(originalparent);
        }
        //没有物品的话就直接然自己进去
        else if (eventData.pointerCurrentRaycast.gameObject.name == "slot(Clone)")
        {
            transform.SetParent(eventData.pointerCurrentRaycast.gameObject.transform);
            transform.position = eventData.pointerCurrentRaycast.gameObject.transform.position;
        }
        //都不是的话，说明拖到其他地方去了，直接回弹
        else
        {
            transform.SetParent(originalparent);
            transform.position = originalparent.position;
        }
        //拖拽完毕后，就可以监听了 以便下次拖拽
        GetComponent<CanvasGroup>().blocksRaycasts = true;
}
```

​	然后在相应的背包格预制体中添加组件Canvas Group，来监听鼠标射线。**此射线只会检测UI组件。**

​	**注意：这里有个小坑，在你拖拽的时候，鼠标是处于物品上方的，射线被物品挡住，无法监听到物品后面的背包格信息，直到拖拽完毕之后。所以在拖拽的时候，要取消物品对鼠标射线的遮挡，要把BlocksRatcasts勾选去掉。**



​	