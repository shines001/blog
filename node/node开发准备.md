
1,  nvm使用  
   nvm是一个安装管理  nodejs的工具，可以方便的在不同的node版本之间切换  

  安装步骤：  
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash  
or Wget:  

wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash  
然后   
source ~/.bashrc  


使用例子  
安装某个版本的node  
nvm  install   6.9.0  
nvm  install   8.5.0  
nvm  install   10.13.0  

使用某个版本的node：  
nvm  use   6.9.0  
注： node 和npm是配对存在的，每一个版本的node都有一个版本的npm与之对应  



2  yarn使用  
安装yarn;  
Yarn是一个代替npm的js包管理工具  

创建新项目流程：  
yarn init  
安装包：  
yarn add package  
yarn add package@version  
移除包：  
yarn  remove package  
安装全部依赖：  
yarn  
yarn install   
