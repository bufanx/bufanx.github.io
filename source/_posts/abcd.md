## leecode算法题

<!--more-->

## leecode1.缺失的数字

####  解法：1

```java
class Solution {
    public int missingNumber(int[] nums) {
     int[] str = new int[nums.length + 1];
        for(int i = 0;i < nums.length;i++){
            str[nums[i]] = 1;
        }
        int j=0;
        for(;j < str.length;j++)
            if(str[j] == 0)
                break;
                return j;
}
}
```

​		因为给出的数据都是从0-n线性排列的 ，中间也只会缺少唯一一个数字，所以我们可以通过重新创造一个数组，让原数字变成在新数组中的下标位置，如果新数组下标与已知数字其中一个相同，则赋值为1，最后再遍历数组，返回值为空的元素的下标位置，即缺失的数字。

#### 解法：2

```java
class Solution {
    public int missingNumber(int[] nums) {
     int res = 0;
		for (int i = 0; i < nums.length; i++) {
			res ^= nums[i]^i;
			
		}
		
		
		return res^nums.length;

}
}
```

​		通过位运算的操作，在不确定少哪个数字的情况下，对所有的数字和从0-n的数字进行位运算，最终数值和下表都存在的数字位运算后结果为一，最终剩下i,即为缺失的那个数字。

## leecode2.字母异位词分组

``` java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
             HashMap<String,ArrayList<String>> map=new HashMap<>();
        for(String s:strs){
            char[] ch=s.toCharArray();
            Arrays.sort(ch);
            String key=String.valueOf(ch);
            if(!map.containsKey(key))    map.put(key,new ArrayList<>());
            map.get(key).add(s);
        }
        return new ArrayList(map.values());

    }
}
```

​		首先创建一张哈希表，然后遍历每一个数组，将字符串转化为数组后，对数组进行排序，并将排序后的数组作为键。如果哈希表中含有改键，则将该数组添加到对应的列表。否则，创建一张空表，并将该数组添加到空表中，往哈希表中添加对应的键和列表，然后用一个新建的列表来存储哈希表的key和value。

## leecode3.金字塔转换矩阵

``` java
class Solution {
        public boolean pyramidTransition(String bottom, List<String> allowed) {
            if(allowed==null||allowed.size()==0)return false;
            HashMap<String,List<String>> dir=new HashMap<>(16);
            for(String str:allowed)
            {
                String head=str.substring(0,2);
                String ch=str.substring(2,3);
                if(dir.containsKey(head))
                {
                    dir.get(head).add(ch);
                }
                else
                {
                    List<String>p=new ArrayList<>();
                    p.add(ch);
                    dir.put(head,p);
                }
            }
            return(dfs(bottom, "",dir));}
        boolean dfs( String last, String now, HashMap<String,List<String>> dir){
            if(last.length()==2&&now.length()==1) {
                return true;
            }
            if(now.length()==last.length()-1){
                return dfs(now,"",dir);
            }
            int start=now.length();int end=now.length()+2;
            List<String> p=dir.get(last.substring(start, end));
            if(p==null) return false;
            for (int i = 0; i < p.size(); i++) {
                if(dfs(last,now+p.get(i),dir)) {
                    return true;
                }
            }
            return false;
        }
}
```

​		

​		先新建一张哈希表用来存储所有可能构成塔身的字母组合和构成塔身的字母。通过dfs算法，如果下层长度为2，上层长度为1，则递归结束，可以构成金字塔，返回值为真。如果下层塔身的长度是比上层塔身多一，则说明目前为止是可以继续构成金字塔的，继续递归就行。start是新开始检索的地址，如果p不存在，无法构成塔身。

## leecode4.被围绕的区域

``` java
class Solution {
        public void solve(char[][] board) {
            if(board==null||board.length==0){
                return 0;
            }
            int m=board.length;
            int n=board[0].length;
            for(int i=0;i<m;i++){
                for(int j=0;j<n;j++){
                    boolean isEdge=(i==0||j==0||i==m-1||j==n-1);
                    if(isEdge&&board[i][j]=='O'){//边界的'O'
                        helper(board,i,j,m,n);//递归
                    }
                }
            }
            for(int i=0;i<m;i++){
                for(int j=0;j<n;j++){
                    if(board[i][j]=='O'){//除了边界的'O'替换
                        board[i][j]='X';
                    }
                    if(board[i][j]=='*'){//边界的'O'再换回来
                        board[i][j]='O';
                    }
                }
            }
        }

        public void helper(char[][] board,int i,int j,int m,int n){
            if(i<0||j<0||i>=m||j>=n||board[i][j]=='X'||board[i][j]=='*'){
                return;
            }
            board[i][j]='*';
            helper(board,i-1,j,m,n);//上
            helper(board,i+1,j,m,n);//下
            helper(board,i,j-1,m,n);//左
            helper(board,i,j+1,m,n);//右
        }

    }
```

​		首先判断矩阵是否合乎规范，如果矩阵为空或者矩阵只有一行，则返回0；m为矩阵行数，n为矩阵列数。然后遍历矩阵，找到边界上的o。然后从边界上的o分别向上下左右方向搜索。直到超出矩阵范围或者碰到不合乎规则的点（与边界连着的o点或者X点）跳出当层递归。当所有的边界上的o点都搜索完成后，结束helper函数。然后再次遍历数组，把所有o变成X，所有*变成o。

## leecode5.最小路径和

``` java
class Solution {
    public int minPathSum(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) {
            return 0;
        }
        int rows = grid.length, columns = grid[0].length;
        int[][] dp = new int[rows][columns];
        dp[0][0] = grid[0][0];
        for (int i = 1; i < rows; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int j = 1; j < columns; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }
        for (int i = 1; i < rows; i++) {
            for (int j = 1; j < columns; j++) {
                dp[i][j] = Math.min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[rows - 1][columns - 1];
    }
}
```

​		首先判断矩阵是否合乎规范，若矩阵为0或者行数为0或者列数为0，矩阵非法。然后新建一个二维数组，行数列数分别和矩阵相同。新数组的第一行或第一列的数字分别为该行或列前边所有数字的和。然后从第二行第二列遍历数组，数组的值为该位置矩阵的值加上这个数字左边或上边最小的一个数字（因为要开辟一条从左上角到右下角最小的路径，所以这个路径的来路要么是左边要么是右边，只要保证每一步走的路径都是二者中的最小值，那么到最后一个元素的时候也必然是所有元素最小值之和）。因此，最后只需要返回新数组的最后一个数字，就是前边所有数字的最小路径的和。