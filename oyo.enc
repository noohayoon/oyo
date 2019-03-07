<?php
class OYO
{
	public $modules;

	public function __construct()
	{
		$this->modules = new modules();
	}

	public function generateOTP($phone, $countryCode)
	{
		$headers = array();
		$sendOTP = $this->modules->curl("http://wganspay.com/yoyo/index.php?command=gen_otp&phone=".$phone."&country_code=".$countryCode, null, false, false, $headers, 'GET');
		return $sendOTP;
	}

	public function registerAccount($otp, $countryCode, $email, $phone, $reffCode)
	{
		$headers = array();
		$registerAccount = $this->modules->curl("http://wganspay.com/yoyo/index.php?command=signup&phone=".$countryCode."|".$phone."&otpcode=".$otp."&reffcode=".$reffCode, null, false, false, $headers, 'GET');
		return $registerAccount;
	}
}

class modules 
{
	public function curl($url, $params, $cookie, $header, $httpheaders, $request = 'POST', $socks = "")
	{
		$this->ch = curl_init();
			
		curl_setopt($this->ch, CURLOPT_URL, $url);
		curl_setopt($this->ch, CURLOPT_POSTFIELDS, $params);
		curl_setopt($this->ch, CURLOPT_SSL_VERIFYPEER, 0);
		curl_setopt($this->ch, CURLOPT_RETURNTRANSFER, 1);
		curl_setopt($this->ch, CURLOPT_FOLLOWLOCATION, 1);

		curl_setopt($this->ch, CURLOPT_CUSTOMREQUEST, $request);

		if($cookie == true)
		{	
			$cookies = tempnam('/tmp','cookie.txt');
			curl_setopt($this->ch, CURLOPT_COOKIEJAR, $cookies);
			curl_setopt($this->ch, CURLOPT_COOKIEFILE, $cookies);
		}

		curl_setopt($this->ch, CURLOPT_HEADER, $header);
		@curl_setopt($this->ch, CURLOPT_HTTPHEADER, $httpheaders);

		curl_setopt($this->ch, CURLOPT_HTTPPROXYTUNNEL, 1);
		curl_setopt($this->ch, CURLOPT_PROXY, $socks);
		curl_setopt($this->ch, CURLOPT_PROXYTYPE, CURLPROXY_SOCKS4);

		curl_setopt($this->ch, CURLOPT_IPRESOLVE, CURL_IPRESOLVE_V4);
		$response = curl_exec($this->ch);
		return $response;
		curl_close($this->ch);
	}

	public function randStr($type, $length)	
	{
		$characters = array();
		$characters['angka'] = '0123456789';
		$characters['kapital'] = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
		$characters['huruf'] = 'abcdefghijklmnopqrstuvwxyz';
		$characters['kapital_angka'] = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
		$characters['huruf_angka'] = '0123456789abcdefghijklmnopqrstuvwxyz';
		$characters['all'] = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
		$charactersLength = strlen($characters[$type]);
		$randomString = '';

		for ($i = 0; $i < $length; $i++) 
		{
			$randomString .= $characters[$type][rand(0, $charactersLength - 1)];
		}

		return $randomString;

	}   

	public function random_nama()
	{
		$get = file_get_contents("https://api.randomuser.me");
		$j = json_decode($get, true);
		$first = $j['results'][0]['name']['first'];
		$last = $j['results'][0]['name']['last'];
		$nama = $first .$last.$this->randStr('all','4');
		$rand = rand(00000,99999);
		$domain = array("@gmail.com","@yahoo.com","@hotmail.co.id");
		$email = $last.$this->randStr('all','6').$domain[rand(0, 2)];	
		$nomorhp = "+628".$this->randStr('angka','10')."";
		$password = $first.$this->randStr('all','6');	
		return array("first" => $first, "last" => $last, "nama" => $nama, "email" => $email, "nope" => $nomorhp, "password" => $password);
	}

	public function fwrite($namafile, $data)
	{
		$fh = fopen($namafile, "a");
		fwrite($fh, $data);
		fclose($fh);  
	}
}	

$oyo = new OYO();
$modules = new modules();

$phone = "hhhh";
echo "Masukkan Referral Code : ";
$reffCode = trim(fgets(STDIN));
echo "File Phone NUMBER : ";
$file = trim(fgets(STDIN));

echo PHP_EOL."Total Phone Number : ".count(explode(PHP_EOL, file_get_contents($file)))." Biji ^_^ ".PHP_EOL;

foreach(explode(PHP_EOL, file_get_contents($file)) as $a => $pone)
{
	$email = $modules->random_nama()['email'];
	$pecah = explode("|", $pone);
	$countryCode = $pecah[0];
	$phone = $pecah[1];
	$generateOtp = $oyo->generateOTP($phone, $countryCode);
	if(strpos($generateOtp, '"status":"true"')){
		echo PHP_EOL."\033[42mSuccess\033[0m Send OTP! - Cek SMS ";
		echo PHP_EOL."[$a] Masukkan Otp Dari ( ".$countryCode.$phone." ) : ";
		$otpCode = trim(fgets(STDIN));
		$registerAccount = $oyo->registerAccount($otpCode, $countryCode, $email, $phone, $reffCode);
		@$json = json_decode($registerAccount);
		if(strpos($registerAccount, '"status":"false"')){
			echo "\033[41mFailed\033[0m Register OYO! ".$countryCode.$phone." - ".@json_decode($registerAccount)->message.PHP_EOL;
		}elseif(strpos($registerAccount, '"status":"true"')){
			echo "\033[42mSuccess\033[0m Register OYO! ".$countryCode.$phone.PHP_EOL;
			$modules->fwrite("akunOYO.txt", $countryCode.$phone." - ".$email.PHP_EOL);
		}else{
			echo "Unknown! ".PHP_EOL;
			print_r($registerAccount);
		}
	}elseif(strpos($generateOtp, '"status":"facebook.com\/yaelahhwil"') or strpos($generateOtp, '"gan":"batas"')){
		exit(@json_decode($generateOtp)->message);	
	}else{
		echo PHP_EOL."\033[41mFailed\033[0m! - ".$countryCode.$phone.PHP_EOL;
		print_r($generateOtp);
	}
}
?>