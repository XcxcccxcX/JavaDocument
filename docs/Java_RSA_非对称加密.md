# Java RAS 非对称加密
  
> 作者：xcx  
> 时间：2020/7/3  09：43  
  
-----------------  

```Java
 /**
     * 私钥
     */
    private static RSAPrivateKey privateKey;

    /**
     * 公钥
     */
    private static RSAPublicKey publicKey;

    /**
     * 字节数据转字符串专用集合
     */
    private static final char[] HEX_CHAR= {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'a', 'b', 'c', 'd', 'e', 'f'};


    /**
     * 获取私钥
     * @return 当前的私钥对象
     */
    public static RSAPrivateKey getPrivateKey() {
        return privateKey;
    }

    /**
     * 获取公钥
     * @return 当前的公钥对象
     */
    public static RSAPublicKey getPublicKey() {
        return publicKey;
    }

    /**
     * 随机生成密钥对
     */
    public void genKeyPair(){
        KeyPairGenerator keyPairGen= null;
        try {
            keyPairGen= KeyPairGenerator.getInstance("RSA");
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }
        keyPairGen.initialize(1024, new SecureRandom());
        KeyPair keyPair= keyPairGen.generateKeyPair();
        privateKey= (RSAPrivateKey) keyPair.getPrivate();
        publicKey= (RSAPublicKey) keyPair.getPublic();
    }

    /**
     * 从文件中输入流中加载公钥
     * @param in 公钥输入流
     * @throws Exception 加载公钥时产生的异常
     */
    public static void loadPublicKey(InputStream in) throws Exception{
        try {
            BufferedReader br= new BufferedReader(new InputStreamReader(in));
            String readLine= null;
            StringBuilder sb= new StringBuilder();
            while((readLine= br.readLine())!=null){
                if(readLine.charAt(0)=='-'){
                    continue;
                }else{
                    sb.append(readLine);
                    sb.append('\r');
                }
            }
            loadPublicKey(sb.toString());
        } catch (IOException e) {
            throw new Exception("公钥数据流读取错误");
        } catch (NullPointerException e) {
            throw new Exception("公钥输入流为空");
        }
    }


    /**
     * 从字符串中加载公钥
     * @param publicKeyStr 公钥数据字符串
     * @throws Exception 加载公钥时产生的异常
     */
    public static void loadPublicKey(String publicKeyStr) throws Exception{
        try {
            BASE64Decoder base64Decoder= new BASE64Decoder();
            byte[] buffer= base64Decoder.decodeBuffer(publicKeyStr);
            KeyFactory keyFactory= KeyFactory.getInstance("RSA");
            X509EncodedKeySpec keySpec= new X509EncodedKeySpec(buffer);
            publicKey= (RSAPublicKey) keyFactory.generatePublic(keySpec);
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException e) {
            throw new Exception("公钥非法");
        } catch (IOException e) {
            throw new Exception("公钥数据内容读取错误");
        } catch (NullPointerException e) {
            throw new Exception("公钥数据为空");
        }
    }

    /**
     * 从文件中加载私钥
     * @param in 私钥输入流
     * @return 是否成功
     * @throws Exception
     */
    public static void loadPrivateKey(InputStream in) throws Exception{
        try {
            BufferedReader br= new BufferedReader(new InputStreamReader(in));
            String readLine= null;
            StringBuilder sb= new StringBuilder();
            while((readLine= br.readLine())!=null){
                if(readLine.charAt(0)=='-'){
                    continue;
                }else{
                    sb.append(readLine);
                    sb.append('\r');
                }
            }
            loadPrivateKey(sb.toString());
        } catch (IOException e) {
            throw new Exception("私钥数据读取错误");
        } catch (NullPointerException e) {
            throw new Exception("私钥输入流为空");
        }
    }

    public static void loadPrivateKey(String privateKeyStr) throws Exception{
        try {
            BASE64Decoder base64Decoder= new BASE64Decoder();
            byte[] buffer= base64Decoder.decodeBuffer(privateKeyStr);
            PKCS8EncodedKeySpec keySpec= new PKCS8EncodedKeySpec(buffer);
            KeyFactory keyFactory= KeyFactory.getInstance("RSA");
            privateKey= (RSAPrivateKey) keyFactory.generatePrivate(keySpec);
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此算法");
        } catch (InvalidKeySpecException e) {
            throw new Exception("私钥非法");
        } catch (IOException e) {
            throw new Exception("私钥数据内容读取错误");
        } catch (NullPointerException e) {
            throw new Exception("私钥数据为空");
        }
    }

    /**
     * 加密过程
     * @param publicKey 公钥
     * @param plainTextData 明文数据
     * @return
     * @throws Exception 加密过程中的异常信息
     */
    public static byte[] encrypt(RSAPublicKey publicKey, byte[] plainTextData) throws Exception{
        if(publicKey== null){
            throw new Exception("加密公钥为空, 请设置");
        }
        Cipher cipher= null;
        try {
            cipher= Cipher.getInstance("RSA/ECB/PKCS1Padding", new BouncyCastleProvider());
            cipher.init(Cipher.ENCRYPT_MODE, publicKey);
            byte[] output= cipher.doFinal(plainTextData);
            return output;
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此加密算法");
        } catch (NoSuchPaddingException e) {
            e.printStackTrace();
            return null;
        }catch (InvalidKeyException e) {
            throw new Exception("加密公钥非法,请检查");
        } catch (IllegalBlockSizeException e) {
            throw new Exception("明文长度非法");
        } catch (BadPaddingException e) {
            throw new Exception("明文数据已损坏");
        }
    }

    /**
     * 解密过程
     * @param privateKey 私钥
     * @param cipherData 密文数据
     * @return 明文
     * @throws Exception 解密过程中的异常信息
     */
    public static byte[] decrypt(RSAPrivateKey privateKey, byte[] cipherData) throws Exception{
        if (privateKey== null){
            throw new Exception("解密私钥为空, 请设置");
        }
        Cipher cipher= null;
        try {
            System.out.println("解密过程中");
            cipher= Cipher.getInstance("RSA/ECB/PKCS1Padding", new BouncyCastleProvider());
            cipher.init(Cipher.DECRYPT_MODE, privateKey);
            byte[] output= cipher.doFinal(cipherData);
            return output;
        } catch (NoSuchAlgorithmException e) {
            throw new Exception("无此解密算法");
        } catch (NoSuchPaddingException e) {
            e.printStackTrace();
            return null;
        }catch (InvalidKeyException e) {
            throw new Exception("解密私钥非法,请检查");
        } catch (IllegalBlockSizeException e) {
            throw new Exception("密文长度非法");
        } catch (BadPaddingException e) {
            throw new Exception("密文数据已损坏");
        }
    }


    /**
     * 字节数据转十六进制字符串
     * @param data 输入数据
     * @return 十六进制内容
     */
    public static String byteArrayToString(byte[] data){
        StringBuilder stringBuilder= new StringBuilder();
        for (int i=0; i<data.length; i++){
            //取出字节的高四位 作为索引得到相应的十六进制标识符 注意无符号右移
            stringBuilder.append(HEX_CHAR[(data[i] & 0xf0)>>> 4]);
            //取出字节的低四位 作为索引得到相应的十六进制标识符
            stringBuilder.append(HEX_CHAR[(data[i] & 0x0f)]);
            if (i<data.length-1){
                stringBuilder.append(' ');
            }
        }
        return stringBuilder.toString();
    }


    //Test
    public static void main(String[] args){
        //加载公钥
        try {
            loadPublicKey(new FileInputStream("/home/public.key"));
            System.out.println("加载公钥成功");
        } catch (Exception e) {
            System.err.println(e.getMessage());
            System.err.println("加载公钥失败");
        }

        //加载私钥
        try {
            loadPrivateKey(new FileInputStream("/home/private.key"));
            System.out.println("加载私钥成功");
        } catch (Exception e) {
            System.err.println(e.getMessage());
            System.err.println("加载私钥失败");
        }

        //测试字符串
        String encryptStr= "JavaOfXcx";
        System.out.println("加密前:");
        System.out.println(encryptStr);
        try {
            //加密
            byte[] cipher = encrypt(getPublicKey(), encryptStr.getBytes());
            //解密
            byte[] plainText = decrypt(getPrivateKey(), cipher);
            BASE64Encoder encode = new BASE64Encoder();
            String buffer= encode.encode(cipher);
            System.out.println("加密后:");
            System.out.println(new String(buffer));
            System.out.println("解密后:");
            System.out.println(new String(plainText));
        } catch (Exception e) {
            System.err.println(e.getMessage());
        }
    }


//例子 私钥 公钥
String privateKey =    "MIINxAIBADANBgkqhkiG9w0BAQEFAASCDa4wgg2qAgEAAoIDAQCqtRM6f2fiUaa8
                        kmiB9L3WRm/AJZRJ5HccvhjQDMVy5Xa8xwe5s+zAYfhK/94r6FS4yhEMNLSL3uDi
                        N23IxiBoye/rXl8pdJq5kALIcI7zznmXB8ncuI2L6AND3kAn3Zo2dKQwzco1WAGr
                        cIxIkAqp8AJez/dlC6biftfUFY0GheT4oLbyS9kAj8TJgymtNjxURKFRCY++nj1j
                        Rlw8SwJuP35c/WueDQd4MdSVnmwE/E6G7PUVz2EhylxJEU7BmVubQHPmUdYuvP4v
                        xyOiJYxoCKq/n5DkKuFQRIE6nZL2PWxiUmlrjKKHnivlXBFQ7SRdLspvQ/2NR3eg
                        5Re/fYxtFf59siEZpqJIzwYWqExGMXHMbOwiL8jLsENbL2PeI65kirfzztkGiZyG
                        rtuxceiOzhuyNrQsOtBsAYgZuHNhG4ouOp92uNLipIEXly0hkrONBwmDAellkY4+
                        zC0/9nlsIzbCTO9pQmfxsVP1UYkgGA/HwFJhTF0H4THrtk0jRqteCJlMzg1FjN/e
                        E0pkPXKXon5zYKiYtgJnYAtrpHzcHiLjcxQktaUEcvbD+6O+Zu0okUYHTmKdjQtw
                        dgHJ2PgmhEBV5L20netoXuMTFVu8rOprCItB/j6+nlMYj2Uq8h1lDCY54x38VGKg
                        5KKH3PSXwpWJps99iD5N7/oE8PAhJXdQB9I3sdFqbn5fYWH5Jh797t5i8OdbLa86
                        nM9WOkm0J3TZ+LiSyJ0YIEKK7gVifuyQZZ6e9tUIzLNXCraFG/QE4Kzh2a61fnBW
                        4+Pa0KpeaAhp40WW9Cu2E+aouhMdfd5yXfStcmnO/VUZB96zNb0s6dRWnwkTR4DV
                        OtPSsp09+/YSxYcVz6apQirT03ndo8ZqGuzHzlI1lQbt31WfJO7pzRNtmNR/ZGJa
                        sQkGCDqQhhxvZdV6EwNiKMZ1AHACnA97ALKfc/AduOLHDOFtgbNrw5y6hOkCRbDO
                        3ekqvSxthgCOTCHQ3piFrHlXNm6ZRUaYmrUi0pskkV0+2B4Lws0CAwEAAQKCAwEA
                        np+4CGmPPKwmxZ8+YVPsewnkmaXRz3/udtTl9Z2IdCVpWi2Pri3U10eyEu8DocU3
                        xKZvhOoMwtQOH+blquUABJ9ww0NkJf9mLvv0uhBtpXu9XGwuVV1gmhqzvgLtlp3C
                        yMtGLttrBHqMwqPIlzzRy+tsKHITLt/O6TR4lWiZLPCKPcYJecilEnKFp8KrnXqC
                        QMOtKsI5wiUEYhVla9k/nvZ9EyehMrJkuHmFUqptMYzJ43KYovWbCm0hp/vfNOKu
                        TutrRx/QaInRoM/o+qjteVZkY7+AQaTezVl6w97cStf0uMBfmUKLXH3LyErvBEX3
                        vmbWUOHa38cDEI0Qri2ZqVwAqQg23cELl6BXxgIJLkiPuCeWnIXOjgdx729v22FI
                        kcCdAn65B+wjeLtPBeoU8W3wKn/OmDLmrRYXQ98zx45xrldnCkjgHR5Dnoops8K/
                        +VIKsqO6H1lL1mqfuYnsdmGaZp5a8XJ1aA8hNxnW0+3H7BN3+VWeNM47Nw6lSxxE
                        aGVSQf1/DohSrUQ86gr66gjEukyuJLSb/PaLQ7npiGAQH5UI394OTVfFBTwAt8IC
                        dG5H/Lllkn7WgQetvAd55PZRWCDoS6F/gULEii9nP12nFwpZwwKXhUzxmdsOo+oX
                        S9+twSPUf3zqFTWutNh3mobH6mPYo/7IDgTjvXTuENAmNbiF31wlM67K+dOem8dD
                        v8UPHNghsol+/wVq6A1vpWgfz6+OJ7spogA8enqZs03g+pNkMy2X5W44aRcSTzfC
                        q4tJqoci1NeJXVpy3HhOSdAF4F6PLXplX8pkjDyT4NcPLM5ymyeeiZvFLOH4L77/
                        Pya65sZoE2tHTZ7ElWYziTE84q+aqFdP+RpeMusI9EwYr95oiN5SOvqxIfyIuoPi
                        sgMyYzotVWZUoYqXCLzeQimbF8zmJI/B0WALDBxx8LZyuP6rRVj9KqrCnrhYlayv
                        pONznxxcm9QoHjHPitUU+csK3e9u1QZZKwGPzYcHu9FBIPAdl7aKwuXZV2wPAkGB
                        AoIBgQDXUHtz4JzoKxLEk/OO/yz8TIDvBFrgWtjgJGB4UiMtHclXTdFgViQAWVEq
                        PptW+8yr/TPU5upoia2q1wGh27Cr9cwgGbWq1T9oLiRc57wJSIh3Ye57USx167yt
                        xTN5/GznrELv5q4GgVKx7govBOENq+9mfMr1ugkuLq3vxXmXjZ/b0cx3WByjgVw7
                        x7a47HpQM1Bz1UhCJM83VUgBtkAIWTCHk9Ci6rlB5pm394Abx8RdfUc1RYlvSAx1
                        HX/qghcPpcABwJDuKTN1GPv+L/Tr/r5eNpYa4j6JivzC512Sjy9Nya/FEXGLk8bb
                        wPuPR2IFD9JT5R4u6iQW+6xrLkiCZwgAQmvnzjK/WzgW086lW8zCRyKcWfwVopqf
                        Q7f73CuQuzfC5Je45qthBI/kUBj8EsSbUNs0aaEIrqG8BAThx2ngaaHh0ldvZtmQ
                        9AraPs9xaQq3/3ZpwMXcZjUWHOFyG/Ll9vvoC+YRYlXKwVC4P6RCALnMc5sDSLzG
                        AC7WtD0CggGBAMr2yrERjZse9NocAyBwvPSVNiU9ltFKIlVOngkRIu7ISVbam9Lu
                        UkxNsjlxaFWmfcqODK+Na3Y2KyrykWWi8OEvmWctfWeYe9S7G6k0p6aozQspGhNk
                        nQRADGNJnbk6yRtoAK2d4TQo/0LHVBe9Zb5oH8whdFv876qTZKxVdeQkGYMhmjP+
                        2jMWvUNlGnnrAGIhBVZ26GiMPF32AoK6+UGRy4YPmIOqQx9e/Jw3mjHKfBQeJW2J
                        IM9CKFo/oEsbmYheuk4XDyZRwZlc3hAvohNWhD/D/jQ0WgIetWyD0jKqnfETj6s9
                        DeHb2jCit8i4tkiw/u+CdUWXfRu2bAAeQGfsgrV22c2fSRWJH5hlSnPLHqPr7zMN
                        HOgCCzKUI3SjB4IKOxTuMkSL0GIflhThpKpRrJZC+a1tbb7ODpObv7HGOvt2wRaH
                        84z33DD7MBb+OrYlxOPCIDFsV8W5IbhXwPgioPyKdxlJjZk2WB2+d4QmCoPLhDzJ
                        WFGS5c/QLk3h0QKCAYEApxW8h2KxQHVUfhm18qzQkwUnNNPzVZEKJX31IsnSpEsu
                        GEK6DQErtN2a36Zv02NZI8o8c6WyF+dnTmDE0n7yLa9zdSGeWXBcYRMVgscNo0KX
                        K9ViRG3si6Gg7FRQqwQY2vtRgmtHdqLaslrfqjcmEf7vq1+B/IgeYak1rxBWWCY9
                        /E5lVeOZbcSP94/2mrBgBmabsxe6mCGKcA0M8M8mB5R21W7+g76UfrBdb2ZwEp7G
                        Iip6nLtWeHW3vRZkUm4bSTg9tN5jWX29gcwemNVMQeqQffnsJ/aTwxaJKRJ0Cax1
                        b+7oKIxtyXum4Jd0X25sgTMS66mr74og8XjiBtaGzDL6AYGJzPu1Y8t8zjIVdTq0
                        vbqIAD7QIVXqmVbqqlbjs8+k6OeNWZ4fNg1dQDZr/Qjvavum75hcr6kctxODWlXu
                        MoimZ+Bbm4Z7pUHMPippvj9eYwSqNkyy/mKOJZfJ10wbBRvRxfOd2LWvj8TOR/yT
                        EekQRbkcfMLwQtKXhmOdAoIBgE4S5SF7+Rbkr5d/EwzVkTocc7mbmXpkpBRgq5Yd
                        S2zDCsMoUKyxFGNZt+c04sefxd+3CNY29lGAwNZCfP+10CcvYjk4XHcPRwMr/pX2
                        NU98u3NBmlA/cc8CvEEtPkjUfivWs/wVMV4ZLygG+SgwqQS3lRO3AsWn5KGfFSjd
                        rv3VjSLOOD0sGc9xPdjA+ZBQf9M/lIgQMZKV71rNmWWkeuFoLfwh3682PZ/BsDZ/
                        hQcGNvieKBOcnkxbzJ36v7Rkp4i3t772S9OXu3s9KAbd6+C4dSL6R7zZLo6GNY/K
                        nX7z9tGXjrp2P/LT2Xi//yZtN2F7BHYpnuboQS353E2nVWskpZscXugkD78DAm5i
                        +GLWjbDMvzTKJIZy0s/gAEuLswWo3dVNU3Teu4gjUl4x9l+2D0e198lowMCXDzBk
                        xzahZGr64YfDQELHzaHh3jvaC8epe7WJJU0duh3K+1eoGgjZeUfsE6hcjOWU7Ax1
                        ChCBeaX9EZ84bIrkkRaBpG49IQKCAYEAi02Xq6cLarwN6B+UDqcESS63diaoKjFx
                        4mD0AvHXT8fiJdoSuamxeUzcJt/tAs5FPQAHb6/yBiXS5+7ZmAM3UbqKsfQknga9
                        uHEZ7q/pWvEq9rG8X2y9AnXVq3vwPdgGGx7EUIUIK/tqbNpnZ3pNimArvpQzrCX2
                        nXm/lTXIs4Zi8gitIIfaWI/oMnumtQVTRViG+V1YJHuiVWF2KhfWQZaHexURId3E
                        S+Rf0Bw4mqzZKfQHHWI04sifXfDtyYlNG07e3lAt61gTb3ttJWuZJK4n/w4yqQNH
                        D7xnsCV/klSr6STbo06zrxzIquGgsLNfZ/pBzShwuQueJsP3rkK6P11FvP4AxxRL
                        vamBoGxtXE7Gbf8JamPBJ18ee6/Y/I3Ynr5VEue4+um+v33gXknUSPY33GxGdoJv
                        28jgHVNav3a/EkFvNfodNYDVzU64gw2+a37gzUWAFjoR/UCCexk0bRY8yEqDrdKO
                        A4Vi2eLVvPPd24kq3tvq2aHlNX6wGEqc";

String publicKey  =    "MIIDIjANBgkqhkiG9w0BAQEFAAOCAw8AMIIDCgKCAwEAqrUTOn9n4lGmvJJogfS9
                        1kZvwCWUSeR3HL4Y0AzFcuV2vMcHubPswGH4Sv/eK+hUuMoRDDS0i97g4jdtyMYg
                        aMnv615fKXSauZACyHCO8855lwfJ3LiNi+gDQ95AJ92aNnSkMM3KNVgBq3CMSJAK
                        qfACXs/3ZQum4n7X1BWNBoXk+KC28kvZAI/EyYMprTY8VEShUQmPvp49Y0ZcPEsC
                        bj9+XP1rng0HeDHUlZ5sBPxOhuz1Fc9hIcpcSRFOwZlbm0Bz5lHWLrz+L8cjoiWM
                        aAiqv5+Q5CrhUESBOp2S9j1sYlJpa4yih54r5VwRUO0kXS7Kb0P9jUd3oOUXv32M
                        bRX+fbIhGaaiSM8GFqhMRjFxzGzsIi/Iy7BDWy9j3iOuZIq3887ZBomchq7bsXHo
                        js4bsja0LDrQbAGIGbhzYRuKLjqfdrjS4qSBF5ctIZKzjQcJgwHpZZGOPswtP/Z5
                        bCM2wkzvaUJn8bFT9VGJIBgPx8BSYUxdB+Ex67ZNI0arXgiZTM4NRYzf3hNKZD1y
                        l6J+c2ComLYCZ2ALa6R83B4i43MUJLWlBHL2w/ujvmbtKJFGB05inY0LcHYBydj4
                        JoRAVeS9tJ3raF7jExVbvKzqawiLQf4+vp5TGI9lKvIdZQwmOeMd/FRioOSih9z0
                        l8KViabPfYg+Te/6BPDwISV3UAfSN7HRam5+X2Fh+SYe/e7eYvDnWy2vOpzPVjpJ
                        tCd02fi4ksidGCBCiu4FYn7skGWenvbVCMyzVwq2hRv0BOCs4dmutX5wVuPj2tCq
                        XmgIaeNFlvQrthPmqLoTHX3ecl30rXJpzv1VGQfeszW9LOnUVp8JE0eA1TrT0rKd
                        Pfv2EsWHFc+mqUIq09N53aPGahrsx85SNZUG7d9VnyTu6c0TbZjUf2RiWrEJBgg6
                        kIYcb2XVehMDYijGdQBwApwPewCyn3PwHbjixwzhbYGza8OcuoTpAkWwzt3pKr0s
                        bYYAjkwh0N6Yhax5VzZumUVGmJq1ItKbJJFdPtgeC8LNAgMBAAE=";

```