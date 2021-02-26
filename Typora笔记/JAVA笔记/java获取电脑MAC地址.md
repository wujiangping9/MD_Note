```java
public String getPCMACAddress() {
    InetAddress inetAddress = InetAddress.getLocalHost();
    byte[] mac = NetworkInterface.getByInetAddress(inetAddress).getHardwareAddress();
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < mac.length; i++) {
        if (i != 0) {
            sb.append("-");
        }
        int temp =  mac[i] & 0xff;
        String str = Integer.toHexString(temp);
        if (str.length() == 1) {
            sb.append("0");
        }
        sb.append(str);
    }
    String MAC = sb.toString().toUpperCase();
    System.out.println(MAC);
    return MAC;
}
```

