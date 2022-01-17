- 双指针

  ~~~ java
  public boolean isPalindrome(String s) {
      String str = s.toLowerCase();
      int i = 0, j = str.length()-1;
      while (i <= j) {
          // 当字符不为a-z && 0-9时，跳过该字符
          if ((str.charAt(i) > 122 || str.charAt(i) < 97) && (str.charAt(i) > 57 || str.charAt(i) < 48)) {
              i++;
              continue;
          }
          if ((str.charAt(j) > 122 || str.charAt(j) < 97) && (str.charAt(j) > 57 || str.charAt(j) < 48)) {
              j--;
              continue;
          }
          if (str.charAt(i) != str.charAt(j)) {
              return false;
          } else {
              i++;
              j--;
          }
      }
      return true;
  }
  ~~~

  