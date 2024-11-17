>[!NOTE] 
>
>自行定义的数据类型
>
>允许存储不同类型的数据项,称为成员;
>
>也可以定义函数;(不建议,如需定义函数,应使用函数)

---
***多说无益,直接上代码***

## basis

#### 示例:
```c++
struct player

{
    int ID;
    int MP;
    int HP;
    int EP;
    bool WhoIsYourDaddy;
    int X,Y;
}player2;!!!注目!!!在这里声明了player2!!!
  
int main(int argc, char const *argv[])

{
    // 第一种
    player player0;
    player0.ID=0;
    player0.MP=100;
    player0.HP=100;
    player0.EP=0;
    player0.WhoIsYourDaddy=false;
    player0.X=0;
    player0.Y=0;
  

    // 第二种
    player player1={1,200,80,0,false,0,0};


    // 第三种
    player2.ID=0;
    player2.MP=100;
    player2.HP=100;
    player2.EP=0;
    player2.WhoIsYourDaddy=false;
    player2.X=0;
    player2.Y=0;

    return 0;
}
```

上述代码示例构建一个 `player` 数据类型;
包含 `int` 和 `bool` 类型;
以及三种构建方法;

---


## 结构体数组

#### 示例:
```c++
// 构建数组

    struct player playerarr[4];

    playerarr[0]=player0;
    
    playerarr[1]=player1;

    playerarr[2]=player2;

    playerarr[3].ID=0;
    playerarr[3].MP=100;
    playerarr[3].HP=100;
    playerarr[3].EP=0;
    playerarr[3].WhoIsYourDaddy=false;
    playerarr[3].X=0;
    playerarr[3].Y=0;

  

    for (int i = 0; i < std::size(playerarr); i++)
    {
        std::cout << "####玩家信息####" << '\n'
                  << "ID: " << playerarr[i].ID     << '\n'
                  << "MP: " << playerarr[i].MP     << '\n'
                  << "HP: " << playerarr[i].HP     << '\n'
                  << "EP: " << playerarr[i].EP     << '\n'
                  << "WhoIsYourDaddy: " << playerarr[i].WhoIsYourDaddy     << '\n'
                  << "X: "  << playerarr[i].X   << '\n'
                 << "Y: "  << playerarr[i].Y   << std::endl;
    }
    return 0;

}
```

### `struce` 包含函数;
#### 示例:
```c++
struct player

{

    player(int id,int mp,int hp,int ep,bool IAmYourDaddy,int initX,int initY) : ID(id),MP(mp),HP(hp),EP(ep),WhoIsYourDaddy(IAmYourDaddy),X(initX),Y(initY){};

    int ID;

    int MP;

    int HP;

    int EP;

    bool WhoIsYourDaddy;

    int X,Y;

    void Move(int& moveX,int& moveY){

        X += moveX;

        Y += moveY;

    };

};

int main(int argc, char const *argv[])

{

    player player0(0,100,100,0,0,0,0);

    int x = 1;

    int y = 2;

    player0.Move(x,y);

  

    std::cout   << "####玩家信息####" << '\n'

                << "ID: " << player0.ID     << '\n'

                << "MP: " << player0.MP     << '\n'

                << "HP: " << player0.HP     << '\n'

                << "EP: " << player0.EP     << '\n'

                << "WhoIsYourDaddy: " << player0.WhoIsYourDaddy     << '\n'

                << "X: "  << player0.X   << '\n'

                << "Y: "  << player0.Y   << std::endl;

  

    return 0;

}
```
---

### 结构体的嵌套与指针 

#### 指针
示例:
```c++
player* pr_playerarr =&playerarr[0];

  

    for (int i = 0; i < 4;i++)

    {

        std::cout << "####Player####" << '\n'

         << "ID: " << pr_playerarr->ID   << '\n'

         << "MP: " << pr_playerarr->MP   << '\n'

         << "HP: " << pr_playerarr->HP   << '\n'

         << "EP: " << pr_playerarr->EP   << '\n'

         << "WhoIsYourDaddy: " << pr_playerarr->WhoIsYourDaddy   << '\n'

         << "X: " << pr_playerarr->X  << '\n'

        << "Y: " << pr_playerarr->Y  << std::endl;

  

        std::cout << sizeof(player) << '\t' << pr_playerarr << std::endl;

        pr_playerarr++;

        std::cout << pr_playerarr << std::endl;

    }
```
#### 嵌套
示例:
```c++

```


---
# `class` 和 `struct` 只有一个区别: `class` 中默认访问限制为 `private` , `struct` 默认访问限制为 `public`