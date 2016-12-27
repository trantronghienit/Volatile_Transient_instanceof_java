# Volatile là gì ?
+ volatile(biến động) khi mỗi lần chạy nó sẽ kiểm tra lại coi biến đó có thay đổi hay không , vậy biến thường với volatile khác nhau thế nào ?
 đó là biến thường thì chỉ kiểm tra giá trị 1 lần duy nhất còn biến volatile thì luôn làm 1 khâu đó là kiểm tra nên biến volatile sẽ chậm hơn biến thường
 

## Tại Sao phải dùng ?
+ Biến volatile trong Java có tác dụng thông báo sự thay đổi giá trị của biến tới các thread khác nhau nếu biến này đang được sử dụng trong nhiều thread.

## dùng Thế nào ?
+ code vidu

```
public class BienVolate {
	private volatile static int COUNT = 0;
	 
    public static void main(String[] args) {
        new ChangeListener().start();
        new ChangeMaker().start();
    }
 
    //thread thứ nhất (ChangeMaker) có tác dụng thay đối giá trị của biến COUNT, 
    //còn thread thứ hai (ChangeListener) sẽ lắng nghe sự thay đổi đó.
    //Biến COUNT sẽ được khai báo là biến volatile để có thể thông báo được sự thay đổi của nó tới thread ChangeListener.
    //Như các bạn thấy, mỗi khi giá trị của biến COUNT thay đổi thì thread ChangeListener sẽ cập nhập sự thay đổi đó liền.

    //Nếu bây giờ mình bỏ khai báo biến COUNT là biến volatile đi thì nó sẽ thông báo 1 lần duy nhất 
    static class ChangeListener extends Thread {
        @Override
        public void run() {
            int value = COUNT;
            // Khi giá trị của biến COUNT nhỏ hơn 5, thread này sẽ lặp mãi mãi để kiểm tra giá trị của biến này
            while (value < 5) {
                if (value != COUNT) {
                    System.out.println("Count variable changed to : " + COUNT);
                    value = COUNT;
                }
            }
        }
    }
 
    static class ChangeMaker extends Thread {
        @Override
        public void run() {
            int value = COUNT;
            while (COUNT < 5) {
                System.out.println("Increasing value of count variable to " + (value + 1));
                COUNT = ++value;
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

}
kết quả khi COUNT là biến volatile:
Increasing value of count variable to 1
Count variable changed to : 1
Increasing value of count variable to 2
Count variable changed to : 2
Increasing value of count variable to 3
Count variable changed to : 3
Increasing value of count variable to 4
Count variable changed to : 4
Increasing value of count variable to 5
Count variable changed to : 5

kết quả khi COUNT không phải là biến volatile:
Increasing value of count variable to 1
Count variable changed to : 1
Increasing value of count variable to 2
Increasing value of count variable to 3
Increasing value of count variable to 4
Increasing value of count variable to 5
```


# Transient là gì ?
+ Chỉ định rằng nếu một đối tượng được serialized(tuần tự), giá trị của biến sẽ không cần được lưu trữ

## Tác dụng 
+ giá trị của biến sẽ không cần được lưu trữ khi serialized

+ code vidu
```
public class Student implements Serializable {
 
    private static final long serialVersionUID = 1L;
    private String name;
    private transient int age;     
 
    public String getName() {
        return name;
    }
 
    public void setName(String name) {
        this.name = name;
    }
 
    public int getAge() {
        return age;
    }
 
    public void setAge(int age) {
        this.age = age;
    }
}
/////////////////main/////////////////
// Tạo đối tượng Student trước
        Student student = new Student();
        student.setName("Hien");
        student.setAge(21);
//////////// ghi xuống file và đọc lên lại /////////     
thì kết quả sẽ là: 
Hien
0               // đã set tuổi là 20 nhưng đc chỉ định là  transient nên nó sẽ không được lưu giá trị và sử dụng giá trị mặc định
```

