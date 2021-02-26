# [java图形界面](https://www.cnblogs.com/mrdz/p/7716643.html)



## 组件

JAVA的图形界面下有两组控件，一组是awt,一组是swing。 
一般都是使用swing

### 1 标签

Label用于显示文字

```java
import java.awt.Color;

import javax.swing.JFrame;
import javax.swing.JLabel;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);
        f.setLayout(null);
        JLabel l = new JLabel("LOL文字");
        //文字颜色
        l.setForeground(Color.red);
        l.setBounds(50, 50, 280, 30);

        f.add(l);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321104245205?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 2 使用JLabel显示图片

java GUI 显示图片是通过在label上设置图标实现的

```java
import javax.swing.ImageIcon;
import javax.swing.JFrame;
import javax.swing.JLabel;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 200);
        f.setLayout(null);

        JLabel l = new JLabel();

        //根据图片创建ImageIcon对象
        ImageIcon i = new ImageIcon("e:/project/j2se/shana.png");
        //设置ImageIcon
        l.setIcon(i);
        //label的大小设置为ImageIcon,否则显示不完整
        l.setBounds(50, 50, i.getIconWidth(), i.getIconHeight());

        f.add(l);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

### 3 按钮

JButton 普通按钮

```java
import javax.swing.JButton;
import javax.swing.JFrame;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);
        f.setLayout(null);
        JButton b = new JButton("一键秒对方基地挂");
        b.setBounds(50, 50, 280, 30);

        f.add(b);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

### 4 复选框

JCheckBox 复选框

使用isSelected来获取是否选中了

```java
import javax.swing.JCheckBox;
import javax.swing.JFrame;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 200);
        f.setLayout(null);
        JCheckBox bCheckBox = new JCheckBox("物理英雄");
        //设置 为 默认被选中 
        bCheckBox.setSelected(true);
        bCheckBox.setBounds(50, 50, 130, 30);
        JCheckBox bCheckBox2 = new JCheckBox("魔法英雄");
        bCheckBox2.setBounds(50, 100, 130, 30);
        //判断 是否 被 选中 
        System.out.println(bCheckBox2.isSelected());

        f.add(bCheckBox);
        f.add(bCheckBox2);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321104608972?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### ５单选框

JRadioButton 单选框 
使用isSelected来获取是否选中了 
为了实现只能选中一个，还需要用到ButtonGroup

```java
import javax.swing.JCheckBox;
import javax.swing.JFrame;
import javax.swing.JRadioButton;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 200);
        f.setLayout(null);
        JRadioButton b1 = new JRadioButton("物理英雄");
        // 设置 为 默认被选中
        b1.setSelected(true);
        b1.setBounds(50, 50, 130, 30);
        JRadioButton b2 = new JRadioButton("魔法 英雄");
        b2.setBounds(50, 100, 130, 30);

        System.out.println(b2.isSelected());

        f.add(b1);
        f.add(b2);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

### 6 按钮组

ButtonGroup 对按钮进行分组，把不同的按钮，放在同一个分组里 ，同一时间，只有一个 按钮 会被选中

```java
import javax.swing.ButtonGroup;
import javax.swing.JFrame;
import javax.swing.JRadioButton;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 240);
        f.setLayout(null);
        JRadioButton b1 = new JRadioButton("物理英雄");
        b1.setSelected(true);
        b1.setBounds(50, 50, 130, 30);
        JRadioButton b2 = new JRadioButton("魔法 英雄");
        b2.setBounds(50, 100, 130, 30);

        // 按钮分组
        ButtonGroup bg = new ButtonGroup();
        // 把b1，b2放在 同一个 按钮分组对象里 ，这样同一时间，只有一个 按钮 会被选中
        bg.add(b1);
        bg.add(b2);

        f.add(b1);
        f.add(b2);
        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321105000103?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 7 下拉框

JComboBox 下拉框 
使用getSelectedItem来获取被选中项 
使用setSelectedItem() 来指定要选中项

```
import javax.swing.JComboBox;
import javax.swing.JFrame;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 240);
        f.setLayout(null);

        //下拉框出现的条目
        String[] heros = new String[] { "卡特琳娜", "库奇" };
        JComboBox cb = new JComboBox(heros);

        cb.setBounds(50, 50, 80, 30);

        f.add(cb);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321105108146?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 8 对话框

JOptionPane 用于弹出对话框

JOptionPane.showConfirmDialog(f, “是否 使用外挂 ？”); 
表示询问，第一个参数是该对话框以哪个组件对齐 
JOptionPane.showInputDialog(f, “请输入yes，表明使用外挂后果自负”); 
接受用户的输入 
JOptionPane.showMessageDialog(f, “你使用外挂被抓住！ “); 
显示消息

```java
mport javax.swing.JFrame;
import javax.swing.JOptionPane;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(580, 240);
        f.setLayout(null);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);

        int option = JOptionPane.showConfirmDialog(f, "是否 使用外挂 ？");
        if (JOptionPane.OK_OPTION == option) {
            String answer = JOptionPane.showInputDialog(f, "请输入yes，表明使用外挂后果自负");
            if ("yes".equals(answer))
                JOptionPane.showMessageDialog(f, "你使用外挂被抓住！ 封号一年！");
        }

    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321112405723?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 9 文本框

JTextField 输入框 
setText 设置文本 
getText 获取文本 
JTextField 是单行文本框，如果要输入多行数据，请使用JTextArea

tfPassword.grabFocus(); 表示让密码输入框获取焦点

```java
import java.awt.Dimension;
import java.awt.FlowLayout;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextField;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(new FlowLayout());

        JLabel lName = new JLabel("账号：");
        // 输入框
        JTextField tfName = new JTextField("");
        tfName.setText("请输入账号");
        tfName.setPreferredSize(new Dimension(80, 30));

        JLabel lPassword = new JLabel("密码：");
        // 输入框
        JTextField tfPassword = new JTextField("");
        tfPassword.setText("请输入密码");
        tfPassword.setPreferredSize(new Dimension(80, 30));

        f.add(lName);
        f.add(tfName);
        f.add(lPassword);
        f.add(tfPassword);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
        tfPassword.grabFocus();
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321112722771?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 10 密码框

JPasswordField 密码框 
与文本框不同，获取密码框里的内容，推荐使用getPassword，该方法会返回一个字符数组，而非字符串

```java
import java.awt.Dimension;
import java.awt.FlowLayout;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPasswordField;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(new FlowLayout());

        JLabel l = new JLabel("密码：");
        // 密码框
        JPasswordField pf = new JPasswordField("");
        pf.setText("&48kdh4@#");
        pf.setPreferredSize(new Dimension(80, 30));

        // 与文本框不同，获取密码框里的内容，推荐使用getPassword，该方法会返回一个字符数组，而非字符串
        char[] password = pf.getPassword();
        String p = String.valueOf(password);
        System.out.println(p);

        f.add(l);
        f.add(pf);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321113020164?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 11 文本域

JTextArea：文本域。 
和文本框JTextField不同的是，文本域可以输入多行数据 
如果要给文本域初始文本，通过\n来实现换行效果 
JTextArea通常会用到append来进行数据追加 
如果文本太长，会跑出去，可以通过setLineWrap(true) 来做到自动换行

```java
import java.awt.Dimension;
import java.awt.FlowLayout;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JTextArea;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(new FlowLayout());

        JLabel l = new JLabel("文本域：");

        JTextArea ta = new JTextArea();
        ta.setPreferredSize(new Dimension(200, 150));
        //\n换行符
        ta.setText("抢人头！\n抢你妹啊抢！\n");
        //追加数据
        ta.append("我去送了了了了了了了了了了了了了了了了了了了了了了了了");
        //设置自动换行
        ta.setLineWrap(true);
        f.add(l);
        f.add(ta);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321113250958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 12 进度条

```java
import java.awt.Dimension;
import java.awt.FlowLayout;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JProgressBar;
import javax.swing.JTextArea;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(new FlowLayout());

        JProgressBar pb = new JProgressBar();

        //进度条最大100
        pb.setMaximum(100);
        //当前进度是50
        pb.setValue(50);
        //显示当前进度
        pb.setStringPainted(true);

        f.add(pb);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321113355229?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 13 文件选择器

JFileChooser 表示文件选择器 
使用FileFilter用于仅选择.txt文件

```java
fc.setFileFilter(new FileFilter() {
    public String getDescription() {
        return ".txt";
    }
    public boolean accept(File f) {
        return f.getName().toLowerCase().endsWith(".txt");
    }
});
```

fc.showOpenDialog(); 用于打开文件 
fc.showSaveDialog(); 用于保存文件

```java
import java.awt.FlowLayout;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;

import javax.swing.JButton;
import javax.swing.JFileChooser;
import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.filechooser.FileFilter;

public class TestGUI {

    public static void main(String[] args) {

        final JFrame f = new JFrame("LOL");
        f.setLayout(new FlowLayout());
        final JFileChooser fc = new JFileChooser();
        fc.setFileFilter(new FileFilter() {

            @Override
            public String getDescription() {
                // TODO Auto-generated method stub
                return ".txt";
            }

            @Override
            public boolean accept(File f) {
                return f.getName().toLowerCase().endsWith(".txt");
            }
        });

        JButton bOpen = new JButton("打开文件");

        JButton bSave = new JButton("保存文件");

        f.add(bOpen);
        f.add(bSave);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setSize(250, 150);
        f.setLocationRelativeTo(null);

        f.setVisible(true);

        bOpen.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                 int returnVal =  fc.showOpenDialog(f);
                 File file = fc.getSelectedFile();
                 if (returnVal == JFileChooser.APPROVE_OPTION) {
                     JOptionPane.showMessageDialog(f, "计划打开文件:" + file.getAbsolutePath());
                 }

            }
        });
        bSave.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int returnVal =  fc.showSaveDialog(f);
                File file = fc.getSelectedFile();
                if (returnVal == JFileChooser.APPROVE_OPTION) {
                    JOptionPane.showMessageDialog(f, "计划保存到文件:" + file.getAbsolutePath());
                }
            }
        });

    }
```

![这里写图片描述](http://img.blog.csdn.net/20170321113828701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast) 
}

## 面板

### 1 基本面板

JPanel 即为基本面板 
面板和JFrame一样都是容器，不过面板一般用来充当中间容器，把组件放在面板上，然后再把面板放在窗体上。 
一旦移动一个面板，其上面的组件，就会全部统一跟着移动，采用这种方式，便于进行整体界面的设计

```java
import java.awt.Color;
import java.awt.FlowLayout;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(null);

        JPanel p1 = new JPanel();
        // 设置面板大小
        p1.setBounds(50, 50, 300, 60);
        // 设置面板背景颜色
        p1.setBackground(Color.RED);

        // 这一句可以没有，因为JPanel默认就是采用的FlowLayout
        p1.setLayout(new FlowLayout());

        JButton b1 = new JButton("英雄1");
        JButton b2 = new JButton("英雄2");
        JButton b3 = new JButton("英雄3");

        // 把按钮加入面板
        p1.add(b1);
        p1.add(b2);
        p1.add(b3);

        JPanel p2 = new JPanel();
        JButton b4 = new JButton("英雄4");
        JButton b5 = new JButton("英雄5");
        JButton b6 = new JButton("英雄6");

        p2.add(b4);
        p2.add(b5);
        p2.add(b6);

        p2.setBackground(Color.BLUE);
        p2.setBounds(10, 150, 300, 60);

        // 把面板加入窗口
        f.add(p1);
        f.add(p2);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321123129649?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 2 ContentPanel

JFrame上有一层面板，叫做ContentPane 
平时通过f.add()向JFrame增加组件，其实是向JFrame上的 ContentPane加东西

```java
import javax.swing.JButton;
import javax.swing.JFrame;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);
        f.setLayout(null);
        JButton b = new JButton("一键秒对方基地挂");
        b.setBounds(50, 50, 280, 30);

        f.add(b);
        // JFrame上有一层面板，叫做ContentPane
        // 平时通过f.add()向JFrame增加组件，其实是向JFrame上的 ContentPane加东西
        // f.add等同于f.getContentPane().add(b);
        f.getContentPane().add(b);

        // b.getParent()获取按钮b所处于的容器
        // 打印出来可以看到，实际上是ContentPane而非JFrame
        System.out.println(b.getParent());

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

### 3 SplitPanel

创建一个水平JSplitPane，左边是pLeft,右边是pRight

```java
import java.awt.Color;
import java.awt.FlowLayout;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JSplitPane;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(null);

        JPanel pLeft = new JPanel();
        pLeft.setBounds(50, 50, 300, 60);

        pLeft.setBackground(Color.RED);

        pLeft.setLayout(new FlowLayout());

        JButton b1 = new JButton("盖伦");
        JButton b2 = new JButton("提莫");
        JButton b3 = new JButton("安妮");

        pLeft.add(b1);
        pLeft.add(b2);
        pLeft.add(b3);

        JPanel pRight = new JPanel();
        JButton b4 = new JButton("英雄4");
        JButton b5 = new JButton("英雄5");
        JButton b6 = new JButton("英雄6");

        pRight.add(b4);
        pRight.add(b5);
        pRight.add(b6);

        pRight.setBackground(Color.BLUE);
        pRight.setBounds(10, 150, 300, 60);

        // 创建一个水平JSplitPane，左边是p1,右边是p2
        JSplitPane sp = new JSplitPane(JSplitPane.HORIZONTAL_SPLIT, pLeft, pRight);
        // 设置分割条的位置
        sp.setDividerLocation(80);

        // 把sp当作ContentPane
        f.setContentPane(sp);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321123921807?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 4 JScrollPanel

使用带滚动条的面板有两种方式 
\1. 在创建JScrollPane，把组件作为参数传进去

JScrollPane sp = new JScrollPane(ta);

2 希望带滚动条的面板现实其他组件的时候，调用setViewportView

sp.setViewportView(ta);

```java
import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(null);
        //准备一个文本域，在里面放很多数据
        JTextArea ta = new JTextArea();
        for (int i = 0; i < 1000; i++) {
            ta.append(String.valueOf(i));
        }
        //自动换行
        ta.setLineWrap(true);
        JScrollPane sp = new JScrollPane(ta);

        f.setContentPane(sp);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

### 5 TabbedPanel

```java
import java.awt.Color;
import java.awt.FlowLayout;

import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JTabbedPane;

public class TestGUI {
    public static void main(String[] args) {

        JFrame f = new JFrame("LoL");
        f.setSize(400, 300);
        f.setLocation(200, 200);

        f.setLayout(null);

        JPanel p1 = new JPanel();
        p1.setBounds(50, 50, 300, 60);

        p1.setBackground(Color.RED);

        p1.setLayout(new FlowLayout());

        JButton b1 = new JButton("英雄1");
        JButton b2 = new JButton("英雄2");
        JButton b3 = new JButton("英雄3");

        p1.add(b1);
        p1.add(b2);
        p1.add(b3);

        JPanel p2 = new JPanel();
        JButton b4 = new JButton("英雄4");
        JButton b5 = new JButton("英雄5");
        JButton b6 = new JButton("英雄6");

        p2.add(b4);
        p2.add(b5);
        p2.add(b6);

        p2.setBackground(Color.BLUE);
        p2.setBounds(10, 150, 300, 60);

        JTabbedPane tp = new JTabbedPane();
        tp.add(p1);
        tp.add(p2);

        // 设置tab的标题
        tp.setTitleAt(0, "红色tab");
        tp.setTitleAt(1, "蓝色tab");

        ImageIcon i = new ImageIcon("e:/project/j2se/j.png");
        tp.setIconAt(0,i );
        tp.setIconAt(1,i );

        f.setContentPane(tp);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        f.setVisible(true);
    }
}
```

![这里写图片描述](http://img.blog.csdn.net/20170321124233977?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTElaSE9OR1BJTkcwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 6 CardLayerout

CardLayerout 布局器 很像TTabbedPanel ，在本例里面上面是一个下拉框，下面是一个CardLayerout 的JPanel 
这个JPanel里有两个面板，可以通过CardLayerout方便的切换

```java
import java.awt.BorderLayout;
import java.awt.CardLayout;
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

import javax.swing.JButton;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JTextField;

public class TestGUI {

    public static void main(String[] args) {
        JFrame f = new JFrame("CardLayerout");

        JPanel comboBoxPane = new JPanel();
        String buttonPanel = "按钮面板";
        String inputPanel = "输入框面板";
        String comboBoxItems[] = { buttonPanel, inputPanel };
        JComboBox<String> cb = new JComboBox<>(comboBoxItems);
        comboBoxPane.add(cb);

        // 两个Panel充当卡片
        JPanel card1 = new JPanel();
        card1.add(new JButton("按钮 1"));
        card1.add(new JButton("按钮 2"));
        card1.add(new JButton("按钮 3"));

        JPanel card2 = new JPanel();
        card2.add(new JTextField("输入框", 20));

        JPanel cards; // a panel that uses CardLayout
        cards = new JPanel(new CardLayout());
        cards.add(card1, buttonPanel);
        cards.add(card2, inputPanel);

        f.add(comboBoxPane, BorderLayout.NORTH);
        f.add(cards, BorderLayout.CENTER);

        f.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        f.setSize(250, 150);
        f.setLocationRelativeTo(null);
        f.setVisible(true);

        cb.addItemListener(new ItemListener() {

            @Override
            public void itemStateChanged(ItemEvent evt) {
                CardLayout cl = (CardLayout) (cards.getLayout());
                cl.show(cards, (String) evt.getItem());
            }
        });     
    }

}
```