#Скопировать в README.md после мёрджа контроллера

### Homework 3 kubernetes-security
1. task01
```
kubectl apply -f bob-and-dave.yaml
```
2. task02
```
kubectl apply -f carol-and-other.yaml
```
*Небольшой момент того, как можно улучшить task02:
вывести список пользователей и запустить цикл применения kubectl apply -f carol-and-other.yaml с списком из*

*Лучше использовать helm, конечно. :)*
```
kubectl get sa -n prometheus -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}'
carol
default
```
*и подсталвением переменной вместо значения subjects.name через envsubst.*

*Идея как небольшой shell скрипт для автоматизации действий.* 

3. task03
```
kubectl apply -f jane-and-ken.yaml
```