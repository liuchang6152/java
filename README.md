# java
java抓取网络图片代码（不太成熟，大多数网页可以）
package com.liuchang;

import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.io.Writer;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.net.URLConnection;
import java.net.URLDecoder;
import java.util.ArrayList;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class ImageDownLoad {
	
	 
    // 
    private static final String IMGURL_REG = "<img.*src=(.*?)[^>]*?>";  
    // 
    private static final String IMGSRC_REG = "http:\"?(.*?)(\"|>|\\s+)";
    private static final String IMGSRC_REGS = "https:\"?(.*?)(\"|>|\\s+)";
    
    public static void main(String[] args) {
    	try {
    		String urlStr = "http://yule.sohu.com/";
    		String mainStr = mainStr(urlStr);
    		List<String> list = new ArrayList<String>();
    		 URL url = new URL(urlStr);
    		 String urlResource = getURLSource(url);
    		 Matcher matcher = Pattern.compile("href=['\"]([^'\"]*)['\"]").matcher(urlResource);
    	        while(matcher.find()) {
    	        	list.add(matcher.group());
    	        }
    	        downloadImg(urlStr);
  	        for(int i=0;i<list.size();i++){
  	        	try {
					String urlSon = list.get(i).replaceAll("href=", "").replaceAll("\"", "").replaceAll("'","");
					if(urlSon.endsWith(".html")&&!urlSon.startsWith("http://")){
						urlSon = mainStr + urlSon;
						downloadImg(urlSon);
					}
					if(urlSon.endsWith(".html")&&urlSon.startsWith("http://")){
						
						downloadImg(urlSon);
					}
				} catch (Exception e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
					continue;
				}
  	        	
  	        }
			
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
    
    //获得父页面
    public static String mainStr(String url)  {  
		 String urlStr =url;
		 String a[] = urlStr.split("/");
		 
		 return urlStr.substring(0,urlStr.length()- a[a.length-1].length());
	    }  

	   /** *//** 
	    * @param args 
	    * @throws MalformedURLException  
	    */  
	    @SuppressWarnings({ "rawtypes", "unchecked" })
		public static void downloadImg(String urls) throws Exception    {  
	       URL url = new URL(urls);
	       //URL url = new URL("http://www.baidu.com");
	       String urlStr = getURLSource(url);
	       if(urlStr.indexOf("src=\"/")>0){
	    	   urlStr = urlStr.replaceAll("src=\"/","src=\"http:/" );
	       }
	       if(urlStr.indexOf("src=/")>0){
	    	   urlStr = urlStr.replaceAll("src=/","src=http:/" );
	       }
	        //System.out.println(getURLSource(url).indexOf("src=\"/"));
	      // String urlsource = getURLSource(url);
	        //System.out.println(urlsource);
	        List list = getImageUrl(urlStr);
	        List<String> imageList = getImageSrc(list);
	        Download(imageList);
	        File outFile = new File("d://5.txt");
	        Writer writer = new FileWriter(outFile);
	       
	        writer.write(urlStr);
	        writer.flush();
	        writer.close();
	        //System.out.println(urlsource); 
    	/*String url = new String("http://images.csdn.net/20170823/奥利奥.png".getBytes("utf-8"),"utf-8");
	    	 download(url, "51bi.png","d:\\image\\");*/
	    }  
	      
	    /** *//** 
	     * ͨ����վ����URL��ȡ����վ��Դ�� 
	     * @param url 
	     * @return String 
	     * @throws Exception 
	     */  
	    public static String getURLSource(URL url) throws Exception    {  
	        HttpURLConnection conn = (HttpURLConnection)url.openConnection();  
	        conn.setRequestMethod("GET");  
	        conn.setConnectTimeout(5 * 1000);  
	        InputStream inStream =  conn.getInputStream();  //ͨ����������ȡhtml����������  
	        byte[] data = readInputStream(inStream);        //�Ѷ���������ת��Ϊbyte�ֽ�����  
	        String htmlSource = new String(data);  
	        return htmlSource;  
	    }  
	      
	    /** *//** 
	     * �Ѷ�������ת��Ϊbyte�ֽ����� 
	     * @param instream 
	     * @return byte[] 
	     * @throws Exception 
	     */  
	    public static byte[] readInputStream(InputStream instream) throws Exception {  
	        ByteArrayOutputStream outStream = new ByteArrayOutputStream();  
	        byte[]  buffer = new byte[1204];  
	        int len = 0;  
	        while ((len = instream.read(buffer)) != -1){  
	            outStream.write(buffer,0,len);  
	        }  
	        instream.close();  
	        return outStream.toByteArray();           
	    }  
	    
	    /*** 
	     * ��ȡImageUrl��ַ 
	     *  
	     * @param HTML 
	     * @return 
	     */  
	    private static List<String> getImageUrl(String HTML) {  
	        Matcher matcher = Pattern.compile(IMGURL_REG).matcher(HTML);  
	        List<String> listImgUrl = new ArrayList<String>();  
	        while (matcher.find()) {  
	            listImgUrl.add(matcher.group());  
	        }  
	        return listImgUrl;  
	    }  
	  
	    /*** 
	     * ��ȡImageSrc��ַ 
	     *  
	     * @param listImageUrl 
	     * @return 
	     */  
	    private static List<String> getImageSrc(List<String> listImageUrl) {  
	        List<String> listImgSrc = new ArrayList<String>();  
	        for (String image : listImageUrl) {
	        	//System.out.println(image.indexOf("' "));
	        	if(image.indexOf("src=")>0){
	        		if(image.indexOf("\\?")>0){
	        			image="<"+image.split("src=")[1].split("\\?")[0]+">";
	        		}
	        		if(image.indexOf("\" ")>0){
	        			image="<"+image.split("src=")[1].split("\" ")[0]+">";
	        		}
	        		
	        		
	        	}
	        	
	            Matcher matcher = Pattern.compile(IMGSRC_REG).matcher(image); 
	            Matcher matchers = Pattern.compile(IMGSRC_REGS).matcher(image);
	            try {
					
						while(matcher.find()){
					       listImgSrc.add(matcher.group().substring(0, matcher.group().length() - 1));
					       }
						while(matchers.find()) {
						   listImgSrc.add(matchers.group().substring(0, matchers.group().length() - 1));
						}

				} catch (Exception e) {
					System.out.println(image);
					e.printStackTrace();
				} finally {
					
					continue;
				}  
	        }  
	        return listImgSrc;  
	    }  
	  
	    /*** 
	     * ����ͼƬ 
	     *  
	     * @param listImgSrc 
	     * @throws Exception 
	     */  
	    private static void Download(List<String> listImgSrc) throws Exception { 
	    	int n = 0;
	       // try {  
	            for (String url : listImgSrc) { 
	            	
	            	n=n+1;
	            	try {  String imageName = n + url.substring(url.lastIndexOf("/") + 1, url.length());  
	                URL uri = new URL(url);  
	                InputStream in;
					FileOutputStream fo;
					URLConnection con = uri.openConnection();
					
					con.setRequestProperty("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");
						//in = uri.openStream();
						//uri.o
						//uri.setRequestProperty("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");
					fo = new FileOutputStream(new File(imageName));
					in = con.getInputStream();
	                byte[] buf = new byte[1024];  
	                int length = 0;  
	                System.out.println("开始下载:" + url);  
	                while ((length = in.read(buf, 0, buf.length)) != -1) {  
	                    fo.write(buf, 0, length);  
	                }  
	                in.close();  
	                fo.close();  
	                System.out.println(imageName + "下载完成"); } 
	            	 catch (Exception e) {
	            		
	     	        	e.printStackTrace();
	     	            System.out.println("下载失败"); 
	     	        } 
	            	finally {
						continue;
					}
	            } 
	        //} catch (Exception e) { 
	        	/*e.printStackTrace();
	            System.out.println("下载失败"); */ 
	        //} 
	            System.out.println("全部下载完成");
	    } 
	 
}

