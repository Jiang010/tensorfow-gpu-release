# 處理需要模型需要Release GPU memerory 的情狀
實作截圖
![image](https://user-images.githubusercontent.com/101046093/176831896-450091b1-daf4-43c2-8a08-ce6fcb3e8b4a.png)

利用 multiprocessing  套件來釋放記憶體，
要把multiprocessing 寫在 main function 底下

```python
from multiprocessing import Process, Queue
import tensorflow as tf
from tensorflow.keras.datasets import mnist
from tensorflow.keras.models import load_model
from tensorflow.keras.utils import to_categorical

def run_model(contect):
    
    # Mnist Dataset
    (X_train, Y_train), (X_test, Y_test) = mnist.load_data()
    x_train = X_train.reshape(60000, 1, 28, 28)/255
    x_test = X_test.reshape(10000, 1, 28, 28)/255
    y_train = to_categorical(Y_train)
    y_test = to_categorical(Y_test)
    
    model = load_model("./model/MNIST.h5")
    model.summary()
    
    data = x_train[0:1]
    result = (model.predict(data))
    contect.put([str(result[0])])
    
if __name__ == "__main__":
    
    import os
    """ 關到他媽tensorflow 垃圾 loggin 輸出 """
    os.environ["TF_CPP_MIN_LOG_LEVEL"] = "3"
    # Mnist Dataset
    (X_train, Y_train), (X_test, Y_test) = mnist.load_data()
    x_train = X_train.reshape(60000, 1, 28, 28)/255
    x_test = X_test.reshape(10000, 1, 28, 28)/255
    y_train = to_categorical(Y_train)
    y_test = to_categorical(Y_test)

  
    D = x_train[0:1]
    
    for i in range(5):
        q = Queue()  ## 建立溝通序列
        
        process_eval = Process(target=run_model, args=(q,))  
        ## 把要執行的 func 丟到 子執行續 ，args 可以帶參數進去子執行續的func
            
        process_eval.start()
        
        print(q.get())  ## 獲得 子執行續的輸出
        
        process_eval.join()    
```

另外一個建立多個執行續的寫法

```python
# 建立存放執行序的list(存放thread)
threads = []

# 放入執行序
t = threading.Thread(target=job, args=('Nash',)) # 
threads.append(t) # 將程序放入threads

# 開始
for t in threads:
    t.start()

# 等待所有子執行緒結束
for t in threads:
    t.join()
```
