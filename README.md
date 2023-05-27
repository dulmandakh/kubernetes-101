# DevSummit 2023 - Kubernetes 101


## 1. namespace үүсгэх

Эхлээд **kubernetes-101** нэртэй namespace үүсгэх хэрэгтэй үүний тулд доорх тушаалыг ашиглаарай

```sh
kubectl apply -f namespace.yaml
```

Үүссэн эсэхийг шалгахдаа доорх тушаалыг ашиглана

```sh
kubectl get namespace kubernetes-101
```

## 2. Pod үүсгэх

Nginx вэб серверийн хамгийн сүүлийн хувилбарыг ашиглан энгийн Pod үүсгэж турших уу. Доорх тушаалыг ашиглана.

```sh
kubectl apply -f nginx-pod.yaml
```

Pod маань үүссэн эсэхийг доорх тушаалыг ашиглаж харна. Ингэхдээ status дээр ContainerCreating гэж байвал контейнэрийн image-ийг татаж авч байна гэсэн үг юм. Интернэтийн хурдаас шалтгаалан удаж магадгүй.

> -n гэж namespace зааж өгч байгаа болно. Зааж өгөөгүй бол default-ийг харуулдаг


```sh
kubectl get po nginx -o wide -n kubernetes-101
```

Running болсны дараа IP хэсэг дээр Kubernetes доторх Pod-ын хаягийг харж болно.

Доорх тушаалын тусламжтай nginx pod-ын 80 гэсэн порт руу 8080 гэсэн портын прокси үүсгэж болно.

```sh
kubectl port-forward pod/nginx 8080:80 -n kubernetes-101
```

Одоо http://127.0.0.1:8080 гэсэн хаягаар Pod руу хандаж болно, nginx хуудас харагдаж байна уу 😎

## 3. Deployment үүсгэх

Энэ удаад **ConfigMap** ашиглан тохиргооны файл үүсгээд тэрийгээ Deployment дамжуулан Pod үүсгэхдээ дамжуулж өгч туршина

Доорх тушаалыг ашиглан **nginx-content** нэртэй ConfigMap үүсгэх бөгөөд index.html гэсэн утганд index.html файлын агуулгыг дамжуулж өгнө.

```sh
kubectl create configmap nginx-content --from-file=index.html=index.html -n kubernetes-101
```

Энэ тушаалыг ашиглан амжилттай үүссэн эсэх, мөн агуулгыг шалгаарай

```sh
kubectl get cm nginx-content -n kubernetes-101 -o yaml
```

Одоо Deployment үүсгэе, доорх тушаалыг ашиглаарай

```sh
kubectl apply -f nginx-deployment.yaml
```

Доорх тушаалаар Deployment үүссэн эсэхийг шалгахаас гадна хэдэн Pod асаасныг харж болно

```sh
kubectl get deploy nginx -n kubernetes-101 -o wide
```

Харин үүссэн Pod жагсаалтыг харахдаа, **nginx-** гэж эхэлсэн урт нэртэй Pod-ууд үүссэн байгаа

```sh
kubectl get pod -n kubernetes-101
```

```sh
kubectl port-forward deployment/nginx 8080:80 -n kubernetes-101
```

## 4. Service үүсгэе

Доорх тушаалыг ашиглан **nginx** нэртэй сервис үүсгэнэ

```sh
kubectl apply -f nginx-service.yaml
```

Энэ тушаалыг ашиглаж үүссэн эсэхийг шалгаж болно

```sh
kubectl get service nginx -n kubernetes-101 -o wide
```

Өмнөхийн ажил 8080 порт дээр nginx сервисийн 80 портыг прокси хийж үзье.

```sh
kubectl port-forward service/nginx 8080:80 -n kubernetes-101
```

## 5. Ingress

Ingress үүсгэхдээ доорх тушаалыг ашиглана.

```sh
kubectl apply -f nginx-ingress.yaml
```

Эцэст нь http://kubernetes-101.45.117.32.201.nip.io хаягаар ороход бидний вэб хуудас харагдаж байгаа
