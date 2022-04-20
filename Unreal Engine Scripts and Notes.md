# Unreal Engine Scripts and Notes

EN: Please contact me to make changes or to share elsewhere. If you have questions or improvements, I'd be happy to hear. 
TR: Lütfen değişiklik yapmak için veya başka yerlerde paylaşmak için iletişime geçiniz. Sorularınız veya geliştirmeleriniz varsa duymaktan memnun olurum.

**Github:** [Github](https://github.com/creosB)

**Youtube:** [Youtube](https://www.youtube.com/c/CreosGaming) 

**The channel where we do this and more:** [Twitch](https://www.twitch.tv/creosb)

1) [#Save and Load Game C++](#save-and-load-game-c)
2) [Using DataTable C++](#using-datatable-c)
3) [API/Json Usage](#apijson-usage)
4) [Optimizing](#optimizing)
5) [Environment](#environment)
6) [Garbage Collection on Unreal Engine](#garbage-collection-on-unreal-engine)
7) [Multiplayer Replication - LAG - Package Loss](#multiplayer-replication---lag---package-loss)
8) [Multiplayer Server Type Selection](#multiplayer-server-type-selection)
9) [Load Game Hierarchy](#load-game-hierarchy)
10) [Unreal Engine Diving Levels Course](#unreal-engine-diving-levels-course)
11) [Export Settings](#export-settings)
12) [Save Graphic and Audio Settings BP](#bp-save-graphic-and-audio-settings)
13) [Audio Settings](#audio-settings)
14) [Graphic Settings (with list)](#graphic-settings-with-list)
15) [Graphic Settings (with button)](#graphic-settings-with-button)
16) [Create Game Instance and Save](#create-game-instance-and-save)
17) [Open Next Level](#open-next-level)
18) [Draw Debug Line](#draw-debug-line)
19) [Rotate Turret (2 Methods)](#rotate-turret-2-methods)
20) [Health Box](#health-box)
21) [Obstacle](#obstacle)
22) [Spawner V2](#spawner-v2)
23) [Gun](#gun)
24) [Projectile](#projectile)
25) [Spawner](#spawner)
26) [Movable Platform](#movable-platform)
27) [Grab](#grab)
28) [BP Create Collectable Object](#bp-create-collectable-object)
29) [BP Set Game Options](#bp-set-game-options)
30) [BP Load Options](#bp-load-options)
31) [BP Load Save](#bp-load-save)
32) [BP Create Game Options](#bp-create-game-options)
33) [BP Create Save](#bp-create-save)
34) [Asset Manager](#asset-manager)
35) [Gameplay Ability Plugin](#gameplay-ability-plugin)
36) [Server Type Check](#server-type-check)
37) [Game Start with CMD](#game-start-with-cmd)
38) [Steam Lobby System C++ (GameDevTV Course)](#steam-lobby-system-c-gamedevtv-course)
39) [Puzzle Platform](#puzzle-platform)
40) [Multiplayer lobby (Multiplayer with hamachi or any IP address)](#multiplayer-lobby-multiplayer-with-hamachi-or-any-ip-address)
41) [Character Control](#character-control)
42) [C++ to Blueprint Node](#c-to-blueprint-node)
43) [Widget Animation C++](#widget-animation-c)
44) [Image Render](#image-render)
45) [EOS Crossplay Multiplayer C++](#eos-crossplay-multiplayer-c)
   - [EOS Kurulumu ve Ayarları](#eos-kurulumu-ve-ayarları)
   - [EOS Kullanımı](#eos-kullanımı)
46) [#OOP on Unreal Engine C++](#oop-on-unreal-engine-c)
   - [Abstract - Interface](#abstract---interface)
   - [Event Handling](#event-handling)
      - [Single Delegate](#single-delegate)
      - [Multicast Delegate](#multicast-delegate)
      - [Event](#event)
      - [Event Manager](#event-manager)    


# OOP on Unreal Engine C++
C++ ile nasıl yapıldıktan sonra buradan devam etmenizi tavsiye ederim. Bunun için C++ OOP yazımı inceleyebilirsiniz.

## Abstract - Interface
Sayfalar arasında fonksiyonları ortak kullanmak için oluşturduğumuz sınıfın içerisine yazarak diğer sınıftan erişim sağlayabilirim. Bunu yapmak için cpp'de olan **virtual void IPureFunction() = 0;** yerine Unreal Engine **PURE_VIRTUAL** makrosunu kullanıyor.

```cpp
virtual void MyPureFunction() PURE_VIRTUAL(AYourActor::YourFunction);
```

Yazdığımız fonksiyonun geri dönüş parametresi varsa
```cpp
virtual void MyPureFunction(uint32 Index) PURE_VIRTUAL(AYourActor::YourFunction; uint32 ReturnNumber);
```

Örnek olarak multiplayer bir oyun için interface sınıfı oluşturdum ve bu sınıfın içerisinde olan fonksiyonları game instance içerisinde çağırmak istersem aşağıda olduğu gibi yapabilirim.

```cpp
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "MenuInterface.generated.h"

// This class does not need to be modified.
UINTERFACE()
class UMenuInterface : public UInterface
{
	GENERATED_BODY()
};

/**
 * 
 */
class YOURGAME_API IMenuInterface
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
public:
	// pure virtual function - no implementation
	virtual void Join(uint32 Index) PURE_VIRTUAL(UYourGameInstance::Join; uint32 Index);
	virtual void LoadMainMenu() PURE_VIRTUAL(UYourGameInstance::LoadMainMenu);
	virtual void RefreshServerList() PURE_VIRTUAL(UCryptoGameInstance::RefreshServerList);
};
```

## Event Handling
Bir işlem yapıldıktan sonra sonucu bize geri döndürmesi için delegate kullanırız. Unreal Engine içerisinde 3 tip delegate bulunur. Bunları alt tarafta açıklayacağım.

### Single Delegate
Belirlediğimiz bir işlem yapıldıktan sonra bize bir dönüş yapması için kullanırız. Bu işlem yapılırken **DECLATE_DELEGATE_OneParam(YourDelegateName,ReturnType)** makrosu kullanılır.

Herhangi bir proje başlattığımızda tuşlara bastığımızda tepki vermesi için önceden yazılmış halde kodları gelir. Bu kodları inceleyecek olursak bunlara delegate atanmış olduğunu görürüz. Bundan dolayı biz de yeni tuşlar ekleyeceğimiz zaman aynı şekilde delegate kullanırız. Örneğin;

```cpp
// Action
PlayerInputComponent->BindAction("Jump", IE_Pressed, this, &ACharacter::Jump);
// Axis
PlayerInputComponent->BindAxis(TEXT("MoveForward"), this, &AContraCharacter::MoveForward);
```

**Bir mesaj gönderen ve alan aktör örneği:**

**YourSpeakerActor.h**
```CPP
DECLARE_DELEGATE_OneParam(FMessageDelegate, FString)

// a talker who sends a message through a delegate
	
UCLASS()
class YOURGAME_API ASpeakerActor : public AActor
{
	GENERATED_BODY()
	
private:
// Message
int MessageNumber{1};
// speaker starts timer
void SpeakerTimer();
// Sends a message to listener
void Speak();

public:
FMessageDelegate MessageDelegate;

ASpeakerActor();

virtual void Tick(float DeltaTime) override;

protected:
virtual void BeginPlay() override;
}	
```
**YourSpeakerActor.cpp**
```cpp
#include "YourSpeakerActor.h"

// I didn't write other functions

void AYourSpeakerActor::BeginPlay()
{
	Super::BeginPlay();
	SpeakerTimer();
}

void AYourSpeakerActor::SpeakerTimer(){
	// define the timer
	FTimerHandle Timer;
	// start the speak timer
	GetWorldTimerManager().SetTimer(Timer, this, &AYourSpeakerActor::Speak, 2);
}

// send message to listener
void AYourSpeakerActor::Speak()
{
	MessageDelegate.ExecuteIfBound("Message " + FString::FromInt(MessageNumber));
	MessageNumber++;
	
	SpeakerTimer();
}
```
**YourListenerActor.h**
```cpp
private:
AYourSpeakerActor* Speaker;

public:
YourListenerActor();
	
virtual void Tick(float DeltaTime);
	
protected:
virtual void BeginPlay() override;

// receives a message from speaker
UFUNCTION()
void ReceiveMessage(FString Message);

// called when actor is being removed from level
// it will unbind itself from the delegate
UFUNCTION()
virtual void EndlPlay(const EEndPlayReason::Type);
```
**YourListenerActor.cpp**
```cpp
#include "YourListenerActor.h"
#include "Kismet/GameplayStatics.h"

void AYourListenerActor::BeginPlay()
{
	Super::BeginPlay();
	
	// find actor with tag
	TArray<AActor*> TaggedActors;
	UGameplaySttics::GetAllActorsWithTag(GetWorld(), "Speaker", TaggedActors);
	
	// bind callback function to delegate
	if(TaggedActors.Num() > 0){
		Spaker = Cast<AYourSpeakerActor>(TaggedActors[0]);
		
	// Bind the delegate from speaker
	Speaker->MessageDelegate.BindUObject(this, &AYourListenerAcotr::ReceiveMessage);
	}  
}

// receive message from speaker
void AYourListenerActor::ReceiveMessage(FString Message){
	UE_LOG(LogTemp, Warning, TEXT("Delegate Message: %s"), *Message);
}

// remove bindings
void AYourListenerActor::EndPlay(const EEndPlayReason::Type){
	if(Speaker != nullptr){
	Speaker->MessageDelegate.UnBind();
	}
}
```

### Multicast Delegate
Single Delagete içerisinde sadece bir callback fonksiyonu barındırabilirken multicast delegate birden fazla callback fonksiyon barındırmasını sağlar.

Bu kısımda single delegate konusunda yaptığımız örnek üzerinden yola çıkacak olursak, örnekte bir konuşmacı ve dinleyici bulunuyordu. Biz bu Dinleyici sayısını arttırdığımızda ise sadece bir dinleyicinin çalıştığını görebiliriz. Multicast delegate kullanmamızın ana sebebi de yukarıda olan sorunun çözmek.

**Not:** Single Delegate kullanırken listener sayısını arttırdığımızda, hangisi en son atanmışsa o dinleyici aktif olur ve konsola yanıt verir.

Single Delegate kısmında yaptığımız örneği devam ettirecek olursak, burada makro olarak **DECLATE_MULTICAST_DELEGATE_OneParam(YourDelegateName,ReturnType)** makrosunu kullanıyoruz.

**Bir mesaj gönderen ve alan aktör örneği:**

**YourSpeakerActor.h**
```CPP
// we changed macro
DECLARE_MULTICAST_DELEGATE_OneParam(FMessageDelegate, FString)

// a talker who sends a message through a delegate
	
UCLASS()
class YOURGAME_API ASpeakerActor : public AActor
{
	GENERATED_BODY()
	
private:
// Message
int MessageNumber{1};
// speaker starts timer
void SpeakerTimer();
// Sends a message to listener
void Speak();

public:
FMessageDelegate MessageDelegate;

ASpeakerActor();

virtual void Tick(float DeltaTime) override;

protected:
virtual void BeginPlay() override;
}	
```
**YourSpeakerActor.cpp**
```cpp
#include "YourSpeakerActor.h"

// I didn't write other functions

void AYourSpeakerActor::BeginPlay()
{
	Super::BeginPlay();
	SpeakerTimer();
}

void AYourSpeakerActor::SpeakerTimer(){
	// define the timer
	FTimerHandle Timer;
	// start the speak timer
	GetWorldTimerManager().SetTimer(Timer, this, &AYourSpeakerActor::Speak, 2);
}

// send message to listener
void AYourSpeakerActor::Speak()
{
	// we changed ExecuteIfBound with Broadcast
	// Broadcast will call back all of the callback functions
	MessageDelegate.Broadcast("Message " + FString::FromInt(MessageNumber));
	MessageNumber++;
	
	SpeakerTimer();
}
```
**YourListenerActor.h**
```cpp
private:
AYourSpeakerActor* Speaker;
// we define the delegate handle for remove the broadcast list of callback functions
FDelegateHande DelegateHandle;

public:
YourListenerActor();
	
virtual void Tick(float DeltaTime);
	
protected:
virtual void BeginPlay() override;

// receives a message from speaker
UFUNCTION()
void ReceiveMessage(FString Message);

// called when actor is being removed from level
// it will unbind itself from the delegate
UFUNCTION()
virtual void EndlPlay(const EEndPlayReason::Type);
```
**YourListenerActor.cpp**
```cpp
#include "YourListenerActor.h"
#include "Kismet/GameplayStatics.h"

void AYourListenerActor::BeginPlay()
{
	Super::BeginPlay();
	
	// find actor with tag
	TArray<AActor*> TaggedActors;
	UGameplaySttics::GetAllActorsWithTag(GetWorld(), "Speaker", TaggedActors);
	
	// bind callback function to delegate
	if(TaggedActors.Num() > 0){
		Spaker = Cast<AYourSpeakerActor>(TaggedActors[0]);
		
	// Bind the delegate from speaker
	// we changed BindUObject with AddUObject because we just adding a callback function to the list
	// We are using DelegateHandle field for delete delegate when you want
	DelegateHandle = Speaker->MessageDelegate.AddUObject(this, &AYourListenerAcotr::ReceiveMessage);
	}  
}

// receive message from speaker
void AYourListenerActor::ReceiveMessage(FString Message){
	UE_LOG(LogTemp, Warning, TEXT("Delegate Message: %s"), *Message);
}

// remove bindings
void AYourListenerActor::EndPlay(const EEndPlayReason::Type){
	if(Speaker != nullptr){
	// we changed UnBind with Remove
	Speaker->MessageDelegate.Remove(DelegateHandle);
	}
}
```

### Event
Multicast Delegate'de tüm sınıflar bir broadcast yaratabilir fakat biz sadece belirli sınıf ile sınırlandırmak istiyorsak bir event oluşturmamız gerekli. Bunun için **DECLARE_EVENT_OneParam(TargetClass, YourDelegateName,ReturnType)** makrosunu kullanıyoruz.

**Bir mesaj gönderen ve alan aktör örneği:**

**YourSpeakerActor.h**
```CPP
// we changed macro
DECLARE_EVENT_DELEGATE_OneParam(AYourSpeakerActor,FMessageDelegate, FString)

// a talker who sends a message through a delegate
	
UCLASS()
class YOURGAME_API ASpeakerActor : public AActor
{
	GENERATED_BODY()
	
private:
// Message
int MessageNumber{1};
// speaker starts timer
void SpeakerTimer();
// Sends a message to listener
void Speak();

public:
FMessageDelegate MessageDelegate;

ASpeakerActor();

virtual void Tick(float DeltaTime) override;

protected:
virtual void BeginPlay() override;
}	
```
**YourSpeakerActor.cpp**
```cpp
#include "YourSpeakerActor.h"

// I didn't write other functions

void AYourSpeakerActor::BeginPlay()
{
	Super::BeginPlay();
	SpeakerTimer();
}

void AYourSpeakerActor::SpeakerTimer(){
	// define the timer
	FTimerHandle Timer;
	// start the speak timer
	GetWorldTimerManager().SetTimer(Timer, this, &AYourSpeakerActor::Speak, 2);
}

// send message to listener
void AYourSpeakerActor::Speak()
{
	// we changed ExecuteIfBound with Broadcast
	// Broadcast will call back all of the callback functions
	MessageDelegate.Broadcast("Message " + FString::FromInt(MessageNumber));
	MessageNumber++;
	
	SpeakerTimer();
}
```
**YourListenerActor.h**
```cpp
private:
AYourSpeakerActor* Speaker;
// we define the delegate handle for remove the broadcast list of callback functions
FDelegateHande DelegateHandle;

public:
YourListenerActor();
	
virtual void Tick(float DeltaTime);
	
protected:
virtual void BeginPlay() override;

// receives a message from speaker
UFUNCTION()
void ReceiveMessage(FString Message);

// called when actor is being removed from level
// it will unbind itself from the delegate
UFUNCTION()
virtual void EndlPlay(const EEndPlayReason::Type);
```
**YourListenerActor.cpp**
```cpp
#include "YourListenerActor.h"
#include "Kismet/GameplayStatics.h"

void AYourListenerActor::BeginPlay()
{
	Super::BeginPlay();
	
	// find actor with tag
	TArray<AActor*> TaggedActors;
	UGameplaySttics::GetAllActorsWithTag(GetWorld(), "Speaker", TaggedActors);
	
	// bind callback function to delegate
	if(TaggedActors.Num() > 0){
		Spaker = Cast<AYourSpeakerActor>(TaggedActors[0]);
		
	// Bind the delegate from speaker
	// we changed BindUObject with AddUObject because we just adding a callback function to the list
	// We are using DelegateHandle field for delete delegate when you want
	DelegateHandle = Speaker->MessageDelegate.AddUObject(this, &AYourListenerAcotr::ReceiveMessage);
	}  
}

// receive message from speaker
void AYourListenerActor::ReceiveMessage(FString Message){
	UE_LOG(LogTemp, Warning, TEXT("Delegate Message: %s"), *Message);
}

// remove bindings
void AYourListenerActor::EndPlay(const EEndPlayReason::Type){
	if(Speaker != nullptr){
	// we changed UnBind with Remove
	Speaker->MessageDelegate.Remove(DelegateHandle);
	}
}
```

### Event Manager
Oluşturduğumuz eventlerde brodcast ekleme ve silme işlemlerini yönetebilmemiz için event manager oluşturmamız gereklidir.

**Bir mesaj gönderen ve alan aktör örneği:**

Event Manager oluşturmak için delegate declarations sınıfı ve event manager actor sınıfı oluşturuyoruz.

**DelegateDeclarations.h**
```CPP
#include "UObject/NoExportTypes.h"

// one parameter message event
DECLARE_MULTICAST_DELEGATE_OneParam(FMessageEvent, FString);

	
UCLASS()
class YOURGAME_API ADelegateDeclarations : public AActor
{
	GENERATED_BODY()
public:
	ADelegateDeclarations();

virtual void Tick(float DeltaTime) override;

protected:
virtual void BeginPlay() override;
}	
```

**EventManagerActor.h**
```CPP
#include "YourSpeakerActor.h"
#include "YourListenerActor.h"
#include "Delegates/DelegateInstanceInterface.h"
	
UCLASS()
class YOURGAME_API AEventManagerActor : public AActor
{
	GENERATED_BODY()
	
public:
	AEventManagerActor();
	
	virtual void Tick(float DeltaTime) override;
	
	void AddInvoker(AYourSpeakerActor* Invoker);
	void RemoveInvoker(AYourSpeakerActor* Invoker);
	void AddListener(AYourListenerActor* Listener);
	void RemoveListener(AYourListernerActor* Listener);
	
private:
	UPROPERTY()
	TArray<AYourSpeakerActor*> MessageEventInvoker;
	TMap<AYourListenerActor*, TMap<AYourSpeakerActor*, FDelegateHandle>> MessageEventListeners;


protected:
	virtual void BeginPlay() override;
}	
```

**EventManagerActor.cpp**
```CPP
void AEventManagerActor::AddInvoker(AYourSpeakerActor* Invoker){
	// add new invoker and add all listeners for the event being broadcast
	MessagEventInvokers.add(Invoker);
	for(auto &Element : MessageEvenetListeners)
	{
		FDelegateHandle DelegateHandle = Element.Key->AddToAddToMessageEvenet(Invoker->GetMessageEvenet());
		
		Element.Value.Add(Invoker, DelegateHandle);
	}
}

void AEventManagerActor::RemoveInvoker(AYourSpeakerActor* Invoker){
	// remove all listener from all invokers
	for(auto &Element : MessageEvenetInvokers)
	{
		if(MessageEventListeners[Listener].Contains(Element))
		{
			Element->GetMessageEvent().Remove(MessageEvenetListeners[Listener][Element])
		}
	}
	
	// remove invoker
	MessageEventInvokers.Remove(Invoker);
}

void AEventManagerActor::AddListener(AYourListenerActor* Listener){
	// add new listener and add new listener to  all invokers
	MessagEventInvokers.add(Invoker);
	
	for(auto &Element : MessageEvenetInvokers)
	{
		FDelegateHandle DelegateHandl = Listener->AddToMessageEvent(GetMessageEvent());
		MessageEvenetListeners[Listener].Add(Element, DelegateHandle);
	}
	
	// remove invoker
	MessageEventInvokers.Remove(Invoker);
}

void AEventManagerActor::RemoveListener(AYourListernerActor* Listener){
	// remove all listeners from invoker
	for(auto &Element : MessageEvenetListeners)
	{
		if(Element.Value.Contains(Invoker)){
			
			Invoker->GetMessageEvent().Remove(MessageEventListeners[Listener][Element]);
			
			MessageEventListeners[Listener].Remove(Element);
		}
	}
	
	// remove listener
	MessageEventListeners.Remove(Listener);
}
```

**YourSpeakerActor.h**
```CPP
#include "DelegateDeclarations.h"


UCLASS()
class YOURGAME_API ASpeakerActor : public AActor
{
	GENERATED_BODY()
	
private:
// Event support
FMessageEvent MessageEvent;

// Message
int MessageNumber{1};
// speaker starts timer
void SpeakerTimer();
// Sends a message to listener
void Speak();

public:
FMessageDelegate MessageDelegate;

ASpeakerActor();

virtual void Tick(float DeltaTime) override;

// get the message evenet for this speaker
// return message event
FMessageEvent &GetMessageEvent();

UFUNCTION()
virtual void EndlPlay(const EEndPlayReason::Type);

protected:
virtual void BeginPlay() override;
}	
```
**YourSpeakerActor.cpp**
```cpp
#include "YourSpeakerActor.h"

// I didn't write other functions

void AYourSpeakerActor::BeginPlay()
{
	Super::BeginPlay();
	// add to event manager
	
	TArray<AActor*> TaggedActors;
	
	UGameplayStatics::GetAllActorsWithTag(GetWorld(), "EventManager", TaggedActors);
	
	if(TaggedActors.Num() > 0)
	{
		AEvenetManagerActor* EvenetManager = Cast<AEventManagerActor>(TaggedActors[0]);
		EventManager->AddInvoker(this);
	}
	
	SpeakerTimer();
}

void AYourSpeakerActor::SpeakerTimer(){
	// define the timer
	FTimerHandle Timer;
	// start the speak timer
	GetWorldTimerManager().SetTimer(Timer, this, &AYourSpeakerActor::Speak, 2);
}

// send message to listener
void AYourSpeakerActor::Speak()
{
	// we changed ExecuteIfBound with Broadcast
	// Broadcast will call back all of the callback functions
	MessageDelegate.Broadcast("Message " + FString::FromInt(MessageNumber));
	MessageNumber++;
	
	SpeakerTimer();
}

// remove bindings
void AYourListenerActor::EndPlay(const EEndPlayReason::Type){
	TArray<AActor*> TaggedActors;
UGameplayStatics::GetAllActorsWithTag(GetWorld(), "EventManager", TaggedActors);
	
	if(TaggedActors.Num() > 0)
	{
		AEventManagerActor* EventManager = Cast<AEventManagerActor>(TaggedActors[0]);
		EventManager->RemoveInvoke(this);
	}
}
```
**YourListenerActor.h**
```cpp
#include "DelegateDeclarations.h"

private:
AYourSpeakerActor* Speaker;
// we define the delegate handle for remove the broadcast list of callback functions
FDelegateHande DelegateHandle;

public:
YourListenerActor();
	
virtual void Tick(float DeltaTime);
FDelegateHandle AddToMessageEvent(FMessageEvent MessageEvent);
	
// receives a message
UFUNCTION()
void ReceiveMessage(FString Message);

protected:
virtual void BeginPlay() override;

// called when actor is being removed from level
// it will unbind itself from the delegate
UFUNCTION()
virtual void EndlPlay(const EEndPlayReason::Type);
```
**YourListenerActor.cpp**
```cpp
#include "YourListenerActor.h"
#include "Kismet/GameplayStatics.h"

void AYourListenerActor::BeginPlay()
{
	Super::BeginPlay();
	
	// add to event manager
	TArray<AActor*> TaggedActors;
	UGameplaySttics::GetAllActorsWithTag(GetWorld(), "EventManager", TaggedActors);
	
	// bind callback function to delegate
	if(TaggedActors.Num() > 0){
		AEventManagerActor* EventManager = Cast<AEventManagerActor>(TaggedActors[0]);
		
		EventManager->AddListener(this);
	}  
}

FDelegateHandle AYourListenerActor::AddToMessageEvent(FMessageEvent MessageEvent)
{
	return MessageEvent.AddUObject(this, &AYourListenerActor::ReceiveMessage);
}

// receive message from speaker
void AYourListenerActor::ReceiveMessage(FString Message){
	UE_LOG(LogTemp, Warning, TEXT("Delegate Message: %s"), *Message);
}

// remove bindings
void AYourListenerActor::EndPlay(const EEndPlayReason::Type){
	// remove event manager
		TArray<AActor*> TaggedActors;
UGameplayStatics::GetAllActorsWithTag(GetWorld(), "EventManager", TaggedActors);
	
	if(TaggedActors.Num() > 0)
	{
		AEventManagerActor* EventManager = Cast<AEventManagerActor>(TaggedActors[0]);
		EventManager->RemoveListener(this);
	}
}
```


# EOS Crossplay Multiplayer C++
## EOS Kurulumu ve Ayarları
EOS Kullanmak için öncelikle https://dev.epicgames.com/ adresine girip SDK'yı indiriyoruz. SDK içerisinde samples.sln dosyasını açıp orada ki bilgileri doldurmamız gerekiyor. Bunun için aşağıda ki adımları takip ediyoruz. 
1) "create product" kısmından oyunumuzu oluşturuyoruz.
2) Oluşan ürüne tıklayıp product settings kısmından bize lazım olan client, application, sandboxes, product, deployment bilgilerini alıyoruz.
3) SDK dosyası içinde samples klasörünün içinde sln dosyasında olan bilgileri, siteden aldığımız bilgiler ile dolduruyoruz.
4) Çalıştırıp hata almadığımızı kontrol ediyoruz.
5) İndirdiğimiz SDK içerisinde Tools klasörüne gelip DevAuthTool'u bir klasöre çıkarıyoruz. DevAuthTool.exe'yi çalıştırıp port numarası belirliyoruz ve giriş yapıyoruz. Sonra gelen credential'a herhangi bir isim veriyoruz. Port numarasına bu örnek için 8081 giriyorum.
6) **\YourEngine\Engine\Plugins\Online\OnlineSubsystemEOS** klasörünü kopyalayıp projemizin olduğu klasöre gelip plugin klasörü oluşturduktan sonra içine atıyoruz.
7) İşlem yaptığımız sdk dosyasını, proje dosyaları içerisinde bulunan OnlineSubsystemEOS içerisine EOS klasörü oluşturup içine atıyoruz.

**Not:** Bu bilgileri alırken Clients kısmından bir client yaratmalı ve orada kullanacağımız özellikleri seçmeliyiz. Client yaratırken aynı zamanda bir de policies yaratıyoruz. Bunları yaptıktan sonra yukarıda yazan bilgileri alabilir hale geliyor.

Oyun açıldığında Epic Games Overlay yanında Steam Overlay göstermek ve kimlik yönlendirmesi için dev.epicgames.com içinde proje ayarlarından Identity Providers geliyoruz ve ekle diyip Steam'i şu bilgilerle ekliyoruz:
- steam 
- 480 
- encryption key: ed9386073647cea58b7721490d59ed445723f0f66e7414e1533ba33cd803bdbd
yaptıktan sonra proje dosyasından girip DefaultEngine.ini'ye ekleme yapmamız gerekiyor. Alt tarafta olan linkten ulaşabilirsiniz.
https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/Online/EOS/
Örnek ayarlar. (artifacts kısmında kendi bilgileriniz olacak)
```
[/Script/OnlineSubsystemEOS.EOSSettings]
CacheDir=CacheDir
DefaultArtifactName=GalleryArtifact
TickBudgetInMilliseconds=0
bEnableOverlay=True
bEnableSocialOverlay=True
bShouldEnforceBeingLaunchedByEGS=False
TitleStorageReadChunkLength=0
+Artifacts = (ArtifactName="GalleryArtifact",ClientId="xyza7891fKfNvoQILhvPTlB631ZaBbah",ClientSecret="+rHpsjcNibP1erpdxKhV8o8w2//igooyFCvty3wEMAk",ProductId="88aaaad1dd5d46778c07816012b37730",SandboxId="8d6263d7808746d9adbf88a777da9bd7",DeploymentId="e7a4b13c909447c6955fd0302858c819",EncryptionKey="1111111111111111111111111111111111111111111111111111111111111111")
bUseEAS=True
bUseEOSConnect=True
bMirrorStatsToEOS=True
bMirrorAchievementsToEOS=True
bUseEOSSessions=True
bMirrorPresenceToEAS=True

[OnlineSubsystemEOS]
bEnabled=true
[OnlineSubsystemEOSPlus]
bEnabled=true
[OnlineSubsystemSteam]
bEnabled=true
SteamDevAppId=480
bInitServerOnClient=true

[OnlineSubsystem]
DefaultPlatformService=EOSPlus
NativePlatformService=Steam

[/Script/Engine.GameEngine]
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemEOS.NetDriverEOS",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")
+NetDriverDefinitions=(DefName="GameNetDriver",DriverClassName="OnlineSubsystemSteam.SteamNetDriver",DriverClassNameFallback="OnlineSubsystemUtils.IpNetDriver")

[/Script/OnlineSubsystemEOS.NetDriverEOS]
bIsUsingP2PSockets=true
MaxNetTickRate=60
NetServerMaxTickRate=60
LanServerMaxTickRate=60
NetClientTicksPerSecond=60
bClampListenServerTickRates=true
MaxClientRate=100000
MaxInternetClientRate=100000

[/Script/OnlineSubsystemUtils.OnlineEngineInterfaceImpl]
+CompatibleUniqueNetIdTypes=EOS
+CompatibleUniqueNetIdTypes=EOSPlus

[/Script/Engine.Player]
ConfiguredInternetSpeed=200000
ConfiguredLanSpeed=200000

[/Script/Engine.GameNetworkManager]
TotalNetBandwidth=6000000
MaxDynamicBandwidth=800000
MinDynamicBandwidth=4000

[/Script/OnlineSubsystemUtils.IpNetDriver]
MaxNetTickRate=60
NetServerMaxTickRate=60
LanServerMaxTickRate=60
NetClientTicksPerSecond=60
bClampListenServerTickRates=true
MaxClientRate=100000
MaxInternetClientRate=100000

[/Script/OnlineSubsystemSteam.SteamNetDriver]
NetConnectionClassName="OnlineSubsystemSteam.SteamNetConnection"
MaxNetTickRate=60
NetServerMaxTickRate=60
LanServerMaxTickRate=60
NetClientTicksPerSecond=60
MaxClientRate=100000
MaxInternetClientRate=100000
```

Oyunun uproject dosyasın modülleri eklememiz gerekiyor.
```
		{
			"Name": "OnlineSubsystemSteam",
			"Enabled": true
		},
		{
			"Name": "SteamSockets",
			"Enabled": true
		},
		{
			"Name": "OnlineSubsystemEOS",
			"Enabled": true
		},
		{
			"Name": "EOSShared",
			"Enabled": true
		},
		{
			"Name": "EOSVoiceChat",
			"Enabled": true
		},
		{
			"Name": "OnlineSubsystem",
			"Enabled": true
		},
		{
			"Name": "OnlineSubsystemNull",
			"Enabled": true
		},
		{
			"Name": "OnlineSubsystemUtils",
			"Enabled": true
		}
```
Oyunun build.cs dosyasına kütüphaneleri eklememiz gerekiyor.
```
"OnlineSubsystem", "OnlineSubsystemEOS", "OnlineSubsystemEOSPlus", "OnlineSubsystemSteam", "OnlineSubsystemUtils", "OnlineSubsystemNull"
```

Artık oyunumuzu açıp EOS pluginleri ve Online Subsystem'i aktif hale getirebiliriz.
Aktif hale getirip yeniden başlattıktan sonra "Project Settings" kısmından Online Subsystem EOS'i açıyoruz ve ayarlarını yapıyoruz.
1) Overlay'leri aktif hale getiriyoruz.
2) Artifacts kısmından yeni bir tane oluşturup, yukarı tarafta aldığımız bilgileri dolduruyoruz ve artifact'e isim veriyoruz.
3) Default Artifact'e oluşturduğumuz artifact'in ismini yazıyoruz.

Login olduktan sonra bilginin gelmemesini düzeltmek için: **(4.27 issue)**
fix this bug in PIE, you need to add a few lines of code in the OnlineSubsystemEOS plugin:
OnlineSubsystemEOSTypes.h (Add a new line below 452)
`static void ResumeCallbacks() { FCallbackBase::bShouldCancelAllCallbacks = false; }`
OnlineSubsystemEOS.cpp (Add a new line below 242)
`FCallbackBase::ResumeCallbacks();`

Project Settings Kısmından EOS Plugin'e geliyoruz ve Crossplay Settings'in altında olan tüm ayarları true yapıyoruz.

Tüm kurulumumuzu bu şekilde tamamlamış olduk. Sırada oyun içerisinde oturum oluşturma, oyuna katılma, arkadaş davet etme gibi fonksiyonları nasıl ekleyeceğinizi göstereceğim. Proje halinde kullanımını görmek isterseniz Art Gallery github repository'e bakabilirsiniz.
[Örnek multiplayer menü](./Pics/https://youtu.be/GVvX_9bMIPk)

## EOS Kullanımı
EOS'in kütüphanesi içerisinde olan fonksiyonları kullanmamız için öncelikle game instance oluşturuyoruz. İçerisine oyun boyunca kullanacağımız fonksiyonları yazarak başka sınıflardan (menu widget vb.) gerekli fonksiyonu çağırıyoruz.

**YourGameInstance.h**
```cpp
public:
	UYourGameInstance();

	// Check  
	bool bIsLoggedIn;

	// AUTH  
	void Login();

	// CREATE  
	UFUNCTION(BlueprintCallable)  
	void CreateSession();

	// DELETE  
	UFUNCTION(BlueprintCallable)  
	void DestroySession();

	UFUNCTION()
	void FindSessions();


	// GET ALL FRIENDS LISTS  
	UFUNCTION(BlueprintCallable)  
	void GetFriendList();

	// START SESSION  
	void StartSession();

	// UI  
	UFUNCTION(BlueprintCallable)  
	void InviteUI();  
	UFUNCTION(BlueprintCallable)  
	void FriendsUI();

private:
	// Delegates  
	void OnLoginComplete(int32 LocalUserNum, bool bWasSuccessful, const FUniqueNetId& UserId,  
						 const FString& Error); // AUTH  
	void OnDestroySessionComplete(FName SessionName, bool bWasSuccessful); // DESTROY  
	void OnCreateSessionComplete(FName SessionName, bool bWasSuccessful); // CREATE  
	void OnFindSessionsComplete(bool bWasSuccessful); // FIND  
	void OnReadFriendsListsComplete(int32 LocalUserNum, bool bWasSuccessful, const FString& ListName,  
									const FString& ErrorStr); // GET ALL FRIENDS LISTS  
	void OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type JoinResult);  
	void OnNetworkFailure(UWorld* World, UNetDriver* NetDriver, ENetworkFailure::Type FailureType,  
						  const FString& ErrorString);  
	void OnSessionUserInviteAcceptedBase(const bool bWasSuccessful, const int32 ControllerId, FUniqueNetIdPtr UserId, const FOnlineSessionSearchResult& InviteResult);  
	void OnStartSessionComplete(FName SessionName, bool bWasSuccessful);

	TSharedPtr<class FOnlineSessionSearch> SearchSettings; // SETTINGS
	IOnlineSessionPtr SessionInterface;

	// Host  
	void Host();

	virtual void Init() override;

protected:
	// Subsystem  
	class IOnlineSubsystem* OnlineSubsystem;
```

**YourGameInstance.cpp**
```cpp

static const FName SESSION_NAME = TEXT("Session");
  
UYourGameInstance::YourGameInstance
(const FObjectInitializer& ObjectInitializer)  
{    
   bIsLoggedIn = false;  
}  
  
void UYourGameInstance::Init()  
{  
   Super::Init();  
   OnlineSubsystem = IOnlineSubsystem::Get(); // GET Online Subsystem  
  
   if (OnlineSubsystem != nullptr)  
   {  
      SessionInterface = OnlineSubsystem->GetSessionInterface();  
   }  
  
   Login();  
     
   // When server shutdown  
   if (GEngine != nullptr)  
   {  
      GEngine->OnNetworkFailure().AddUObject(this, &UYourGameInstance::OnNetworkFailure);  
   }  
}  
  
  
// Login function  
void UYourGameInstance::Login()  
{  
   if (OnlineSubsystem)  
   {  
      if (IOnlineIdentityPtr Identity = OnlineSubsystem->GetIdentityInterface())  
      {  
         FOnlineAccountCredentials Credentials;  
  
         Credentials.Id = FString();  
         Credentials.Token = FString();  
         Credentials.Type = FString("accountportal");  
           
         /*  
                  // DEVELOPER LOGIN                  Credentials.Id = FString("127.0.0.1:8081"); // AUTH_LOGIN                  Credentials.Token = FString("CreosCred"); // AUTH password                  // you can use accountportal or developer - you can set other tags -                  Credentials.Type = FString("developer"); // AUTH_TYPE                  
				  */  
         Identity->OnLoginCompleteDelegates->AddUObject(this, &UYourGameInstance::OnLoginComplete);  
         Identity->Login(0, Credentials);  
      }  
   }  
}  
  
// Login function delegate  
void UYourGameInstance::OnLoginComplete(int32 LocalUserNum, bool bWasSuccessful, const FUniqueNetId& UserId,  
                                          const FString& Error)  
{  
   UE_LOG(LogTemp, Warning, TEXT("LoggedIn Result: %d"), bWasSuccessful); // check login  
  
   if (OnlineSubsystem)  
   {  
      if (IOnlineIdentityPtr Identity = OnlineSubsystem->GetIdentityInterface())  
      {  
         // set login info  
         switch (Identity->GetLoginStatus(0))  
         {  
         case ELoginStatus::LoggedIn: bIsLoggedIn = true;  
            Host();        
            break;  
         case ELoginStatus::NotLoggedIn: bIsLoggedIn = false;  
            break;  
         default: bIsLoggedIn = false;  
         }  
  
         // // it will delete delegates when it completed.  
         Identity->ClearOnLoginCompleteDelegates(0, this);  
      }  
   }  
}  
  
// Create Session Function  
void UYourGameInstance::CreateSession()  
{  
   if (OnlineSubsystem && bIsLoggedIn)  
   {  
      // Get SessionInterface function from subsystem  
      if (SessionInterface)  
      {  
         FOnlineSessionSettings SessionSettings;  
  
         // Default Settings  
         SessionSettings.bShouldAdvertise = true; // for invite your friend  
         SessionSettings.bIsDedicated = false;  // is it dedicated server
         SessionSettings.bIsLANMatch = true; // local gameplay is true  
         SessionSettings.NumPublicConnections = 5; // how many people can join the your lobby // this is limit  
         SessionSettings.NumPrivateConnections = 5; // how many people can join the your lobby // this is limit  
         SessionSettings.bAllowJoinInProgress = true; // join permission  
         SessionSettings.bAllowJoinViaPresence = true; // join permission  
         SessionSettings.bUsesPresence = true;  
         SessionSettings.bUseLobbiesIfAvailable = true;  
         SessionSettings.bAllowJoinViaPresenceFriendsOnly = false;  
         SessionSettings.bAllowInvites = true;  
  
         // Set and create lobby info  
         // You can check with "SEARCHKEYWORDS" attribute  key at lobbies section.         SessionSettings.Set(SEARCH_KEYWORDS, FString("GalleryLobby"),  
                             EOnlineDataAdvertisementType::ViaOnlineServiceAndPing);  
  
         // Define the delegate  
         SessionInterface->OnCreateSessionCompleteDelegates.  
                           AddUObject(this, &UYourGameInstance::OnCreateSessionComplete);  
  
         // Create session  
         SessionInterface->CreateSession(0, SESSION_NAME, SessionSettings);  
      }  
   }  
}  
  
// Create Session Delegate  
void UYourGameInstance::OnCreateSessionComplete(FName SessionName, bool bWasSuccessful)  
{  
   UE_LOG(LogTemp, Warning, TEXT("Create Success: %d"), bWasSuccessful);  
  
   if (!bWasSuccessful)  
   {  
      UE_LOG(LogTemp, Warning, TEXT("Session is not created"));  
      return;  
   }  
  
   // it will check is logged in and subsystem is not nullptr  
   if (OnlineSubsystem) // bIsLoggedIn &&   
{  
      if (SessionInterface.IsValid())  
      // Get SessionInterface function from subsystem  
      {  
         if (bWasSuccessful)  
         {  
            // Define the delegate  
            SessionInterface->OnStartSessionCompleteDelegates.  
                              AddUObject(this, &UYourGameInstance::OnStartSessionComplete);  
  
            // Set the StartSession delegate handle  
            StartSession();  
         }  
         // it will delete delegates when it completed.  
         SessionInterface->ClearOnCreateSessionCompleteDelegates(this);  
      }  
   }  
   else  
   {  
      UE_LOG(LogTemp, Error, TEXT("You can't create session: Not Logged In %d"), bWasSuccessful);  
   }  
}  
  
  
// Destroy Session Function  
void UYourGameInstance::DestroySession()  
{  
   if (OnlineSubsystem)  
   {  
      // Get SessionInterface function from subsystem  
      if (SessionInterface)  
      {  
         // define the delegate  
         SessionInterface->OnDestroySessionCompleteDelegates.AddUObject(  
            this, &UYourGameInstance::OnDestroySessionComplete);  
         // it will destroy session.  
         SessionInterface->DestroySession(SESSION_NAME);  
      }  
   }  
}  
  
// Destroy Session Delegate  
void UYourGameInstance::OnDestroySessionComplete(FName SessionName, bool bWasSuccessful)  
{  
   if (OnlineSubsystem)  
   {  
      // Get SessionInterface function from subsystem  
      if (SessionInterface)  
      {  
         // it will delete delegates when it completed.  
         SessionInterface->ClearOnDestroySessionCompleteDelegates(this);  
      }  
   }  
}  
  
// add new server and clear old server list  
void UYourGameInstance::RefreshServerList()  
{  
   if (OnlineSubsystem)  
   {  
      if (SessionInterface.IsValid())  
      {  
         // Find Online Session  
         // MakeShareable c++ pointer convert to reference pointer         SearchSettings = MakeShareable(new FOnlineSessionSearch()); // heap memory  
         // check is it null         if (SearchSettings.IsValid())  
         {  
            SearchSettings = MakeShareable(new FOnlineSessionSearch()); // create new reference  
            SearchSettings->MaxSearchResults = 100;  
            SearchSettings->bIsLanQuery = false;  
            SearchSettings->QuerySettings.Set(SEARCH_KEYWORDS, true, EOnlineComparisonOp::Equals);  
  
            // define the delegate  
            SessionInterface->OnFindSessionsCompleteDelegates.AddUObject(  
               this, &UCryptoGameInstance::OnFindSessionsComplete);  
  
            ULocalPlayer* const Player = GetFirstGamePlayer();  
            // Find session  
            SessionInterface->FindSessions(Player->GetUniqueID(), SearchSettings.ToSharedRef());  
            // get settings ref and find session  
         }  
      }  
   }  
}  
  
  
// Find Session Delegate  
void UYourGameInstance::OnFindSessionsComplete(bool bWasSuccessful)  
{  
   if (bWasSuccessful && SearchSettings.IsValid() && Menu != nullptr)  
   {  
      TArray<FServerData> ServerNames; // server name array  
  
      for (const FOnlineSessionSearchResult& SearchResult : SearchSettings->SearchResults)  
      {  
         UE_LOG(LogTemp, Warning, TEXT("Found session name: %s"), *SearchResult.GetSessionIdStr());  
      }   
  
      // it will delete delegates when it completed.      SessionInterface->ClearOnFindSessionsCompleteDelegates(this);  
      //UE_LOG(LogTemp, Warning, TEXT("Found Sessions: %d"), SearchSettings->SearchResults.Num()); // check  
   }  
}  
  
// join session  
void UYourGameInstance::Join(uint32 Index)  
{  
   if (!SessionInterface.IsValid()) { return; }  
   if (!SearchSettings.IsValid()) { return; }  
   if (Menu != nullptr)  
   {  
      // it's sending server list to MainMenu function.  
      //Menu->SetServerList({"Test1", "Test2", "Test3", "Test4"});   }  
   UE_LOG(LogTemp, Warning, TEXT("Joinning Session"));  
   // define the delegate  
   SessionInterface->OnJoinSessionCompleteDelegates.  
                     AddUObject(this, &UYourGameInstance::OnJoinSessionComplete);  
  
   SessionInterface->JoinSession(0, SESSION_NAME, SearchSettings->SearchResults[Index]);  
}  
  
// Join Session Delegate  
void UYourGameInstance::OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type JoinResult)  
{  
   // Get SessionInterface function from subsystem  
   UE_LOG(LogTemp, Warning, TEXT("Join Session Delegate is working!"));  
   if (OnlineSubsystem)  
   {  
      if (SessionInterface)  
      {  
         FString ConnectionInfo;  
         if (!SessionInterface->GetResolvedConnectString(SessionName, ConnectionInfo)) // get connection info  
         {  
            UE_LOG(LogTemp, Warning, TEXT("Could not get connect string."));  
            return;  
         }  
         // join to the input adress.  
         APlayerController* PlayerController = GetFirstLocalPlayerController();  
         if (!ensure(PlayerController != nullptr)) { return; }  
         PlayerController->ClientTravel(ConnectionInfo, ETravelType::TRAVEL_Absolute); // travel the map  
  
         UE_LOG(LogTemp, Warning, TEXT("Player is travelling"));  
      }  
   }  
}  
  
// when start session  
void UYourGameInstance::StartSession()  
{  
   if (SessionInterface.IsValid())  
   {  
      UE_LOG(LogTemp, Warning, TEXT("Session is start function working..."));  
      SessionInterface->StartSession(SESSION_NAME);  
   }  
}  
  
// Start session delegate  
void UYourGameInstance::OnStartSessionComplete(FName SessionName, bool bWasSuccessful)  
{  
   if (OnlineSubsystem)  
   {  
      if (SessionInterface.IsValid())  
      {  
         // load to the map  
         UWorld* World = GetWorld();  
         if (!ensure(World != nullptr)) { return; }  
         if (AGameModeBase* GameModeBase = Cast<AGameModeBase>(UGameplayStatics::GetGameMode(World)))  
         {  
            if (bWasSuccessful)  
            {  
               UE_LOG(LogTemp, Warning, TEXT("Session is starting..."));  
               GameModeBase->bUseSeamlessTravel = true;  
               World->ServerTravel(TEXT("/Game/Maps/YourMap?listen"));  
            }  
         }  
  
         SessionInterface->ClearOnStartSessionCompleteDelegates(this);  
      }  
   }  
}  
  
// Host for dedicated server  
void UYourGameInstance::Host()  
{  
   if (SessionInterface.IsValid() && bIsLoggedIn)  
   {  
      // auto guess which type and get session  
      auto ExistingSession = SessionInterface->GetNamedSession(SESSION_NAME);  
      // check session is null or not  
      if (ExistingSession != nullptr)  
      {  
         // destroy session  
         SessionInterface->DestroySession(SESSION_NAME);  
      }  
      else  
      {  
         UE_LOG(LogTemp, Warning, TEXT("I'm creating session"));  
         // create session  
         CreateSession();  
      }  
   }  
}  
  
// Get All Friends Lists function  
void UYourGameInstance::GetFriendList()  
{  
   if (bIsLoggedIn && OnlineSubsystem)  
   {  
      if (IOnlineFriendsPtr Friends = OnlineSubsystem->GetFriendsInterface())  
      {  
         Friends->ReadFriendsList(0, FString(""),  
                                  FOnReadFriendsListComplete::CreateUObject(  
                                     this, &UYourGameInstance::OnReadFriendsListsComplete));  
      }  
   }  
}  
  
// Get All Friends Lists Delegate  
void UYourGameInstance::OnReadFriendsListsComplete(int32 LocalUserNum, bool bWasSuccessful, const FString& ListName,  
                                                     const FString& ErrorStr)  
{  
   if (bWasSuccessful && OnlineSubsystem)  
   {  
      if (IOnlineFriendsPtr FriendsPtr = OnlineSubsystem->GetFriendsInterface())  
      {  
         TArray<TSharedRef<FOnlineFriend>> FriendsLists; // friend list reference  
         if (FriendsPtr->GetFriendsList(0, ListName, FriendsLists)) // get all friends lists  
         {  
            for (TSharedRef<FOnlineFriend> Friend : FriendsLists) // find all friend from friendlists  
            {  
               FString FriendName = Friend.Get().GetRealName();  
  
               UE_LOG(LogTemp, Warning, TEXT("Friend Name: %s"), *FriendName); // check  
            }  
         }  
      }  
   }  
}  
  
// Show Invite UI  
void UYourGameInstance::InviteUI()  
{  
   if (bIsLoggedIn && OnlineSubsystem)  
   {  
      if (IOnlineExternalUIPtr ExternalUI = OnlineSubsystem->GetExternalUIInterface())  
      {  
         ExternalUI->ShowInviteUI(0, SESSION_NAME); // invite UI  
      }  
   }  
}  
  
// Show Friends List UI function  
void UYourGameInstance::FriendsUI()  
{  
   if (bIsLoggedIn && OnlineSubsystem)  
   {  
      if (IOnlineExternalUIPtr ExternalUI = OnlineSubsystem->GetExternalUIInterface())  
      {  
         ExternalUI->ShowFriendsUI(0); // friend UI  
      }  
   }  
}  
  
void UYourGameInstance::OnNetworkFailure(UWorld* World, UNetDriver* NetDriver, ENetworkFailure::Type FailureType,  
                                           const FString& ErrorString)  
{  
   // when host left the server
   UE_LOG(LogTemp, Warning, TEXT("Host left this server!!"));
}  
   
void UYourGameInstance::OnSessionUserInviteAcceptedBase(const bool bWasSuccessful, const int32 ControllerId,  
                                                          FUniqueNetIdPtr UserId,  
                                                          const FOnlineSessionSearchResult& InviteResult)  
{  
   if (OnlineSubsystem && bWasSuccessful)  
   {  
      SessionInterface->JoinSession(0, SESSION_NAME, SearchSettings->SearchResults[0]);  
   }  
}
```

Oyunda **dedicated server** kullanacaksanız aldığınız çıktıda olan exe dosyasının kısayolunu oluşturup aşağıda olan komutu eklemeniz gerekiyor. Bunun sayesinde sunucunuz ile oyununuz aynı bilgisayar üzerinde test edebilirsiniz.
```
-server -NOSTEAM -log -AUTH_LOGIN=127.0.0.1:8081 -AUTH_PASSWORD=CreosCred -AUTH_TYPE=developer
```
Oyunu aynı bilgisayar üzerinden oynayarak test etmek için bir metin belgesi oluşturup aşağıda olan komutu ekledikten sonra .bat dosyasına dönüştürüp çalıştırın.
```
start "" "YOURENGINEPATH\Engine\Binaries\Win64\UE4Editor.exe" "YOURGAMEPATH.uproject" -game -WINDOWED -ResX=450 -ResY=450 -NOSTEAM
```

Menü yapımı ve projeye dönüştürülmüş hali için [Örnek multiplayer menü](./Pics/https://youtu.be/GVvX_9bMIPk)

# Image Render
Render alırken aşağıda bulunan ayarlar ile render almak daha iyi sonuç verir.
```
output format: jpg
frame rate: 24fps
width: 5120
height: 2160
```
**r.Tonemapper.Sharpen** komutu ile birlikte 0 ile 5 arasında değer deneyerek en iyi değeri bulursak resim daha güzel görünecektir. **(r.Tonemapper.Sharpen 0.5)**

**HDR için:**

output format: custom render passes
add render passes kısmından: final image'i ekliyoruz.
Capture Frames in HDR: enabled

# Widget Animation C++
Widget c++ sınıfına bağlı olarak oluşturduğumuz widget blueprint içerisinde kullanacağımız buton, overlay vb. araçlar için animasyon oluşturduktan sonra bunu c++ içerisinde aşağıda olduğu gibi çalıştırıyoruz. Bu örnekte bir buton üzerinden gideceğim.

**YourWidget.h**
```cpp
	
private:
	UPROPERTY(meta = (BindWidget))
	class UButton* YourButtonName; // it need to same name with widget

	UPROPERTY(Transient, meta=(BindWidgetAnim, AllowPrivateAccess = true))
	UWidgetAnimation* YourAnimationName; // it need to same name with widget
};
```
**YourWidget.cpp**
```cpp
bool UYourWidget::Initialize()
{
	if (!ensure(YourButtonName != nullptr)) { return false; }
	YourButtonName->OnHovered.AddDynamic(this, &UYourWidget::OnHovered);
	YourButtonName->OnUnhovered.AddDynamic(this, &UYourWidget::OnUnHovered);
	return true;
}
void UYourWidget::OnHovered()
{
	if (YourAnimationName)
	{
		PlayAnimation(SettingsAnim, 0.0f, 1, EUMGSequencePlayMode::Forward, 2.0f, false);
	}
}

void UYourWidget::OnUnHovered()
{
	if (YourAnimationName)
	{
		PlayAnimation(SettingsAnim, 0.0f, 1, EUMGSequencePlayMode::Reverse, 2.0f, false);
	}
}

```

# Save and Load Game C++

Öncelikle C++ oluştururken SaveGame class'ı seçerek oluşturuyoruz. Seçenekler arasında göremezseniz **all hierarcy** butonuna tıkladıktan sonra arama yaparak erişebilirsiniz.

Oyun ayarlarını kaydedeceğim için bu örnek üzerinden gideceğim. Bundan dolayı önceden oluşturduğum struct sınıfını save game dosyama ekliyorum.

**Not:** cpp dosyasına herhangi bir şey eklememize gerek yok.

**YourSaveGameClass.h**
```cpp
#pragma once

#include "CoreMinimal.h"
#include "SettingsDataStruct.h"
#include "GameFramework/SaveGame.h"
#include "YourSaveGame.generated.h"

UCLASS()
class YOURGAME_API UYourSaveGame : public USaveGame
{
	
	GENERATED_BODY()
	
public:
	UYourSaveGame();

	UPROPERTY(VisibleAnywhere, Category = "Save Game Data")
	FSettingsDataStruct* SettingsData;
};
```

Ayarlarımın kaydedilmesi için bir butona bastığımda bu işlemin yapmasını istediğim için butonun tıklanma fonksiyonuna ekliyoruz. Bu örnekten farklı şekilde ilerliyorsanız siz de hangi aktörün hareketi sonucu kayıt edilecekse, o aktörün fonksiyonuna aynı işlemleri uygulayarak yapabilirsiniz.

**YourSettingsWidgetClass.h**
```cpp
#include "SettingsDataStruct.h"

private:
	// Save Game
	class UYourSaveGame* SaveGameData;
```
**YourSettingsWidgetClass.cpp**
```cpp
#include "YourSaveGameClass.h"

MyClass::MyFunc()
{
	if (SettingsDataTable)
	{
		SaveGameData = Cast<UYourSaveGame>(UGameplayStatics::LoadGameFromSlot("YourGameSlot", 0));

		if (SaveGameData != nullptr)
		{
			// retrieve configuration data from data table
			FString ContextString;
			SaveGameData->SettingsData = SettingsDataTable->FindRow<FSettingsDataStruct>("ConfigData", ContextString);
		}
		else
		{
			// couldn't load save game
			SaveGameData = Cast<UYourSaveGame>(UGameplayStatics::CreateSaveGameObject(UYourSaveGame::StaticClass()));

			FString ContextString;
			SaveGameData->SettingsData = SettingsDataTable->FindRow<FSettingsDataStruct>("ConfigData", ContextString);
		}
	}
}
```

Ayarları csv dosyasına kaydettiğim için kayıt dosyasını çağırma işlemi ardından eğer dosya varsa ayarlarımı alıyorum. Kayıt dosyası bulunmuyorsa, kayıt dosyası oluşturduktan sonra ayarlarımı alıyorum.

Not: Tek ve büyük bir veriler ile işlem yapmayacağı için kayıt dosyası oluşturma ve yükleme kısmında asenkron olarak işlem yapmadım. Sizin durumunuzda bu durum farklıysa asenkron olarak işlem yapmayı gözden geçirebilirsiniz.

Örneğimizden devam edecek olursak, herhangi bir ayar değiştirildiğinde bunu oluşturduğumuz kayıt dosyasına eklemek için aşağıdaki gibi işlem yapabiliriz.

**YourSettingsWidgetClass.cpp**
```cpp
MyClass::MyFunc2()
{
	if (SaveGameData && sItem != SaveGameData->SettingsData->VsyncQuality)
		{
			if (Vsync.Num() <= 0)
			{
				Vsync.Add(TEXT("on"), true);
				Vsync.Add(TEXT("off"), false);
			}
			// set save game data variable
			SaveGameData->SettingsData->VsyncQuality = sItem;
		}
}
```

# Using DataTable C++
Alt tarafta göreceğiniz gibi grafik ayarlarının olduğu bir csv dosyam bulunuyor. İşlemlerimi de bu dosya ile yapacağım.

|Name      |SceenResolution|ViewDistance|AntiAliasing|ShadowQuality|TextureQuality|VsyncQuality|MasterVolume|MusicVolume|
|----------|---------------|------------|------------|-------------|--------------|------------|------------|-----------|
|ConfigData|2560x1440      |Far         |Epic        |Ultra        |Ultra         |on          |1.0f        |1.0f       |
|          |1920x1080      |Medium      |High        |High         |High          |off         |            |           |
|          |1280x720       |Near        |Medium      |Medium       |Medium        |            |            |           |
|          |720x480        |            |Low         |Low          |Low           |            |            |           |


Not: İlk sütunun ismi **Name** olmak zorunda.

İlk olarak bir ustruct class oluşturuyoruz. İçerisinde csv dosyamızda olan bilgileri giriyoruz.

**YourStructClass.h
```cpp
#pragma once
#include "Engine/DataTable.h"
#include "SettingsDataStruct.generated.h"

USTRUCT(BlueprintType)
struct FSettingsDataStruct : public FTableRowBase
{
	GENERATED_USTRUCT_BODY()

public:
	FSettingsDataStruct()
		: ScreenResolution("1920x1080")
		,ViewDistance("Far")
		,AntiAliasing("Epic")
		,ShadowQuality("Ultra")
		,TextureQuality("Ultra")
		,VsyncQuality("on")
		,MasterVolume(1.0f)
		,MusicVolume(1.0f)
	{}

	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString ScreenResolution;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString ViewDistance;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString AntiAliasing;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString ShadowQuality;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString TextureQuality;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	FString VsyncQuality;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	float MasterVolume;
	UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Settings Data Stuct")
	float MusicVolume;
};
```


**Content Browser** içerisinde sağ tıklayıp **asset import** diyerek oluşturduğumuz bu csv dosyasını seçiyoruz. Seçim sırasında bize struct sınıfını sorduğunda bir önceki adımda oluşturduğumuz sınıfı seçerek oluştur diyoruz.

Artık oluşturduğumuz bu datatable ile diğer aktörlerde işlemler yapabiliriz.

# API/Json Usage

First of all, you need to add a http and json module.
```cpp
PrivateDependencyModuleNames.AddRange(new string[] { "Http", "Json", "JsonUtilities" });
```
Let’s create an httptest actor.

**HttpTest.h**
```cpp
#include "Http.h"
 
// for POST json section
USTRUCT()
struct FPlayerData
{
  GENERATED_BODY()
  UPROPERTY()
  FString title = "foo";
  UPROPERTY()
  FString body = "bar";
  UPROPERTY()
  int16 userId = 1;
 
};
 
private:   
  FHttpModule* Http;
 
  void PostArea();
  void OnProccessRequestComplete(FHttpRequestPtr Request, FHttpResponsePtr Response, bool Success);
```

**HttpTest.cpp**
```cpp
AHttpTest::AHttpTest()
{
  // Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
  PrimaryActorTick.bCanEverTick = true;
 
  Root = CreateDefaultSubobject<USceneComponent>(TEXT("Root"));
  RootComponent = Root;
 
  Trigger = CreateDefaultSubobject<ATriggerVolume>(TEXT("Trigger"));
 
  Http = &FHttpModule::Get(); // get http module
}
 
// Called when the game starts or when spawned
void AHttpTest::BeginPlay()
{
  Super::BeginPlay();
  PostArea(); // function call
}
 
void AHttpTest::PostArea()
{
     // create request
     TSharedRef<IHttpRequest, ESPMode::ThreadSafe> Request = Http->CreateRequest();
     // bind to object
     Request->OnProcessRequestComplete().BindUObject(this, &AHttpTest::OnProccessRequestComplete);
     // Request Settings - GET -
     Request->SetURL("https://jsonplaceholder.typicode.com/posts"); // request url
     Request->SetVerb("GET"); // request type
     Request->SetHeader("Content-Type", TEXT("application/json")); // set json format
 
     // Request Settings - Post -
     /*
     Request->SetVerb("POST"); // request type
     Request->SetHeader("Content-Type", TEXT("application/json")); // set json format
     FString JsonString;
     FPlayerData PlayerData;
     FJsonObjectConverter::UStructToJsonObjectString(PlayerData,JsonString);
     Request->SetContentAsString(JsonString);
     */
    
     Request->ProcessRequest();
}
 
// print respond message
void AHttpTest::OnProccessRequestComplete(FHttpRequestPtr Request, FHttpResponsePtr Response, bool Success)
{
  if (Success)
  {
     UE_LOG(LogTemp, Warning, TEXT("%s"), *Response->GetContentAsString());
  }
  else
  {
     UE_LOG(LogTemp, Warning, TEXT("Request Failed!!!"));
  }
}
```

# Optimizing

<h3>Identify Key Metrics</h3>

1) What hardware will the end user be using ?
2) Avoid unneeded features and effects
3) Framerate:    
    * VR:90 FPS
    
    * Console/PC: 60 FPS
    
    * Let's visualize and track the problem.

**Note:** If you want to see game fps, you can open with the "Stat fps" command or press down the
arrow button. If you want to see more details about GPU, Draw, Fps etc. you can open with the "Stat unit" command.

<h3>GPU</h3>

**GPU visualizer shortcut:** ctrl + shift + comma

You can see which one affects the GPU with loading duration and you can fix it.

In this case the post process effect -> bloom effect is affecting the GPU for a reason you need to find the post process effect -> bloom -> change the method with standard. Search the point light and delete all of them.

<h3>CPU</h3>

**CPU visualizer command:** Stat SceneRendering

Draw calls meaning it's going to ue source and calling the polygons.

If you want to reduce draw calls you can remove, merge objects, remove extraneous material channel or reduce the amount of shadows being cast by dynamic lights.

<h3>Thread</h3>

If you want the game thread to be the bottleneck you can type "Stat Game". This will show you how many tick operations are occuring on frame in the Viewport.

<h3>Memory</h3>

You can see the **"TEXTURE STREAMING POOL OVER"** warning in the Unreal Engine because we don't
have enough memory allocated to the Unreal Engine to display the highest-resolution textures.

**Let's fix that issue!**

In the Editor, Window -> Statistics -> Click Primitive Stats and select "Texture Stats". Now you can see the fully loaded memory. Find the largest texture and let's start to reduce it. Open the largest texture -> Change "Maximum Texture Size" with power of two values (2048) -> save the texture and back to the statistics window.

**Note:** If any of your textures being used aren't the power of two textures, they can affect your memory usage. Because they don't have a lower mipmap texture to switch. Resolve that issue, open the texture -> in the Texture section, change "Power of Two Mode '' to pad texture to a higher value. Alternative solution is you can edit your texture with simple programs and reimport to Unreal Engine.

Alternative solution of the "Texture Streaming Pool Over", you can allocate more ram with console command. Type "streaming.poolsize amount". Example usage: "streaming.poolsize 8000". Now we can check with the "stat memory" command.

<h3>Visualize the Complexity</h3>

**You can check with:**
1) Light Complexity (are too many overlapping dynamic lights?)
2) Shader Complexity (models are optimize or not)
3) Quad overdraw (you can see each pixels is optimize or not)

<h3>Some Additional Settings</h3>

1) Project Settings -> In the Project section, choose Target Hardware (mobile, pc)
2) Project Settings -> Engine -> Under the framerate, we can select fixed fps if we don't need to streaming extremely high frame rate -> and we can select "Smooth Frame Rate" for help stuttering if you can't avoid frame rate issues in the editor.
3) Project Settings -> Rendering -> Under the Culling section, make sure the "Occlusion Culling" is enabled. (this setting determines whether objects will be hidden or if they are visible to the camera.) -> Scroll down and under the "Reflections", "Reflection Capture Size" be sure under the power two.
4) If you don't need Ray Tracing, Global illumination disables it.

<h3>LOD</h3>

When looking at a model, the model is made up of triangles. You need to make sure that this number of triangles changes when you move away or get closer. This way you don't have to render a model that is too far away in high resolution.

If you want to change LOD settings right click to static mesh -> Details -> Under the LOD Settings, you can change "LOD Group". Example when you selected "SmallProp" you can see your mesh triangle amount is changing for your away or get closer movement.

Now if you want to modify the LOD reduction settings, under the "LOD Picker" section you can enable "custom" option. Now you can scroll down and see the LOD 1, LOD 2, LOD 3 section. Open the LOD 1 reduction settings and modify the amount of decimation you would like.

We can change every mesh LOD settings with new one. Select all mesh and press right click-> Asset Actions -> Bulk Edit via Property Matrix -> Under the LOD Settings, change the LOD Group name with "SmallProp" (case sensitive).

<h3>DataSmith Plugin</h3>

Plugins -> Enable Datasmith Importer Plugin -> Enable Dataprep Editor Plugin

Right click and select Dataprep -> Dataprep Asset -> rename with Dataprep_1

Before adding Unreal Engine, we can look at its preview to import or files to import and set up action that we would like to occur on the file here.

Press the Input + icon -> Choose datasmith file -> Press the import button and wait.

Now you can change for example triangle count. Only you need to do drag and drop the right area and give an amount (select greater than 500). After doing that, add LOD Group Node and select type.If you finished all settings now you can press the execute button and start waiting. When it's done you can press commit to add the model to your viewport and content browser.

<h3>Jacketing</h3>

Plugins -> Enable Polygon Editing

Right click the mesh and select jacketing -> You can see different settings in the pop-up.
1) Voxel prediction determines the precision of the skin.
2) Gap Max Diameter determines how large of gaps we should skip over if any are present.
3) You can choose a level or mesh scan.
    * **Level:** if object are inside the external casing and modify those object that are not seen from any angle outside of the exterior object. (large buildings, vehicles)
    * **Mesh:** it will instead consider occlusion at the level of individual triangles and delete
    polygons that are not visible from the exterior. If we really don't need to render complex objects
    (have a large amount of detail), it will be helpful. You can check on the wireframe mode (for it's
    necessary or not decision).


**Note:** This function is only available on 64 bit windows.

<h3>Auto UV Generation</h3>

When you import the geometry, don't forget the enable "generate lightmap uv" option.

UV0 is the default UV channel that was created to inform how textures would display on the mesh.

UV1 i s for the lightmap.

In the UV section, select unwrap UV option -> if you keep the angle and UV0 was blank, you need to
select specify channel option -> press the proceed button.

If you have the more complicated geometry, you can use the generate UV tool. This tool will allow
more editing options for the UV.

**Note:** You will get the best result in the 3D program for UV


# Environment

<h3>Collision</h3>

When you import static mesh object for example chair and if you want to add collision your own
object, you have 3 methods for this situations.
1) Select object -> edit and add box, sphere or other collision.
2) Select object -> edit and add complex collision with some settings (hull count).
3) Custom collision for your object. In the modelling program add the collison and export. Make sure your own collision to use the UCX_ prefix before the asset you are working with. Also add _01 at the end of the filename for many pieces.

**Example:** **SM_Chair** would be **UCX_SM_Chair_01**

<h3>Material</h3>

Create 3 texture sample for your material.
1) Texture sample for base color. (diffuse)
2) Texture sample for normal. (mask)
3) Texture sample R node for ambient occlusion, G node for roughness, B node for metalic (normal) press the right click and select "convert to parameters" for all of them.

Now we can create material instance. Right click the material and select "create material instance" and rename with MI_ prefix.

<h3>Texture</h3>

When you import the texture, don't forget the check compression settings. For example 3 texture for your object. 1 diffuse, 1 mask, 1 normal map for your object. Mask compression settings it need to be selected "Mask (no rgb)" and other texture need to be default DXT in the editor. You can check with the right click and edit.

Open your material instance where you are created from material. You can see diffuse, mask and
normal section on the right. Just drag and drop correct texture this area.

Now open your static mesh and change your default material to created material instance.


<h3>Light</h3>

**Unreal Engine light types:**

**Directional Light:** Sun or moon light. You can change sun location with Ctrl + L.

**Point Light:** Turn of cast shadow settings in this light because this light type too expensive than others.

**Spot Light:** Most common light type for room.

**Rect Light:** Useful light for tv screen.

**Sky Light:** When you check the light density, you can see some object red or other color. It need to be green or similar color. You can easily change this situation. Click the object and select edit -> right panel general settings in below you can see "Light Map Resolution" -> change the lower value. For example 256 to 128 -> In the editor Build section select "Build Lightning" and wait.
If your screen is too dark, you can change light intensity.

**Extra:** You can use a light profile for your lamp. Select your object -> in the details panel, find light profile -> select IES Texture. In the World Settings you can see the light mass section.
In this section, you can enable ambient occlusion and you can change light quality or set other light settings.

**Lightmass Portal:** for the window opening effect.

**Sphere Reflection Capture:** for the metallic and roughness material.

**Note:** If you want a reflection size you need to open Project Settings -> Rendering -> Reflection -> Reflection Capture Size. For example, change 128 to 256 values. After the changing reflection capture, in the build section -> select build reflection capture.

**Post Process Volumes:**
Add the post process volume and atmospheric fog. You can change game looking style with post process volume. You can think like photo mode in game. You can set black and white, exposure, or other stuff.


# Garbage Collection on Unreal Engine

![Image](./Pics/Unreal-Engine-C--._image1.png)

![Image](./Pics/Unreal-Engine-C--.image4.png)

![Image](./Pics/Unreal-Engine-C--.image27.png)

You can check multiplayer puzzle mechanic video for this.

![Image](./Pics/Unreal-Engine-C--.image13.png)

![Image](./Pics/Unreal-Engine-C--.image9.png)

![Image](./Pics/Unreal-Engine-C--.image40.png)

![Image](./Pics/Unreal-Engine-C--.image23.png)

# Multiplayer Replication - LAG - Package Loss 

<h3>Replication Actor Roles</h3>

![Image](./Pics/Unreal-Engine-C--.image21.png)

![Image](./Pics/Unreal-Engine-C--.image35.png)

![Image](./Pics/Unreal-Engine-C--.image38.png)

<h3>What is the packet loss ?</h3>

![Image](./Pics/Unreal-Engine-C--.image62.png)

![Image](./Pics/Unreal-Engine-C--.image32.png)

![Image](./Pics/Unreal-Engine-C--.image24.png)

<h3>What is the LAG ?</h3>

![Image](./Pics/Unreal-Engine-C--._image2.png)

![Image](./Pics/Unreal-Engine-C--.image30.png)

<h3>Let’s visualize the lag</h3>

![Image](./Pics/Unreal-Engine-C--.image7.png)

![Image](./Pics/Unreal-Engine-C--.image15.png)

<h3>Fix the LAG Methods</h3>

![Image](./Pics/Unreal-Engine-C--.image57.png)

**Solutions:**

![Image](./Pics/Unreal-Engine-C--.image34.png)

**Let’s start!**

![Image](./Pics/Unreal-Engine-C--.image19.png)

<h3>Replication Refactor for movement bindings</h3>

![Image](./Pics/Unreal-Engine-C--.image44.png)

**What is the correct smooth movement graph ?**

![Image](./Pics/Unreal-Engine-C--.image42.png)

![Image](./Pics/Unreal-Engine-C--.image10.png)

![Image](./Pics/Unreal-Engine-C--.image26.png)

![Image](./Pics/Unreal-Engine-C--.image3.png)

![Image](./Pics/Unreal-Engine-C--.image31.png)

![Image](./Pics/Unreal-Engine-C--.image20.png)

<h3>Simulate and make smooth movement</h3>

![Image](./Pics/Unreal-Engine-C--.image36.png)

![Image](./Pics/Unreal-Engine-C--.image29.png)

![Image](./Pics/Unreal-Engine-C--.image17.png)


# Multiplayer Server Type Selection

Comparison for multiplayer game Unreal Engine support.

![Image](./Pics/Unreal-Engine-C--.image6.png)

![Image](./Pics/Unreal-Engine-C--.image37.png)

![Image](./Pics/Unreal-Engine-C--.image12.png)

![Image](./Pics/Unreal-Engine-C--.image18.png)

<h3>Let’s divide section by section</h3>

![Image](./Pics/Unreal-Engine-C--.image14.png)

![Image](./Pics/Unreal-Engine-C--.image16.png)

# Load Game Hierarchy

![Image](./Pics/Unreal-Engine-C--.image22.png)

# Unreal Engine Diving Levels Course 

This method for the load or unload environment.
1) windows -> levels menu
2) levels -> new -> select empty level -> level name C0_sublevel
3) select all meshes -> right click to c0_sublevel on levels menu -> select move

Now when you click the eye icon, all static meshes will hide or unhide.

<h3>Loading Screens</h3>
Options for the loading screens:

1) Fade to solid color ( most common solution but users shouldn't stay in a faded state for more than 5 seconds or
they might think the application has frozen.)

https://docs.unrealengine.com/en-US/BlueprintAPI/CameraFades/StartCameraFade/index.html

2) Show VR splash screen ( Engine-supported, easy to use as long as animation or interaction isn't required during the
loading screen.)

https://docs.unrealengine.com/4.27/en-US/SharingAndReleasing/XRDevelopment/VR/VRHowTos/XRLoadingScreens

3) Show one or more stereo layers ( most powerful option but might also take the most work)

https://docs.unrealengine.com/4.27/en-US/SharingAndReleasing/XRDevelopment/VR/VRHowTos/StereoLayers/

**Example of fade to solid color method:** c0_lobbymap Open Level Blueprint

![Image](./Pics/Unreal-Engine-C--._image39.png)

<h3>Example of VR Splash Screen</h3>
c4_lobby 

BP_TravelTo ( for the switch between levels)

When you move the level load are it will show the splash screen. After wating the splash screen, it will load level.

<h3>Example of Cache method:</h3>

On Quest, shaders are not actually compiled until they are used in a draw call. This causes the first frame to hitch as the first frame gets rendered.

One single shader compilation can cause the app to fail the oculus certification requirement of 13.88 ms.

1) Open the engine/source folder -> search shaderpipelinecache.cpp and open -> Under the FShaderPipelineCache bool method, change FPipelineFileCache::SetGameUsage... to FShaderPipelineCache.

2) In the editor go to edit -> project settings -> set "share shader material code" and "shared material native libraries"
option the true.

3) Windows -> Developer tools -> open device profiles -> click the android -> Under the Console Variables click to Rendering + icon -> search "r.ShaderPipelineCache.Enabled" -> set the value 0 to 1 -> close editor.

4) open Engine/config/android/AndroidEngine -> save and close.

5) In the editor, open the project launcher -> press the + icon -> project section click the browse -> select
project.uproject -> cook section select "by the book" than set true "Android", "Android_ASTC" -> scroll down and
cooked maps section, set true all of the map. -> Scroll down to launch section and under the initial map section ,
select c0_lobby (starting level this course) -> under the "additional command line parameters" type "-logPSO
-NoVerifyGC" -> Scroll up and rename the profile "Android PSO Cache".
Note: Before the pressing the launch this profile button, make sure that you have your target device setup and
connected your dev. PC.

6) Open the command line and write "adb shell mkdir -p yourproject/Saved/CollectedPSOs"

7) Create PSOCaching folder -> open
yourprojectfolder/saved/cooked/android_astc/projectname/metadata/PipelineCaches and copy two
ShaderStableInfo file into created PSOCaching folder.

8) Open the project/saved/CollectedPSOs -> copy UE4-DEV... cache file and paste PSOCaching folder -> open the
engine/binaries/win64 -> Create UE4Editor-Cmd.exe shortcut and move PSOCaching folder -> press the right click
and open the properties than in thhe target section, add Z:/ProjectName -run=ShaderPipelineCacheTools expand
C:/PSOCaching/*.rec.upipelinecache C:/PSOCaching/*.scl.csv ProjectName_GLSL_ES3_1_ANDROID.stablepc.csv and
press the ok button -> run the shortcut file -> it will create csv file on this folder. (if it didn't create file on this folder,
you need the check engine binaries folder.)

9) Open the PSOCaching folder and copy ProjectName_GLSL_ES3_1.stablepc.csv file -> open the
ProjectName/build/Android folder and create PipelineCaches -> paste the file in here
1
0) In the editor, build with File-> PackageProject -> Android -> Android (ASTC)

**Recommendations:** Check in the stablepc.csv file into source control , there is no need to update this file during
development.

**Note:** Before product ships or major milestones, re-run steps 2 to 5 to update the stablepc.csv file to make sure all
shaders used are included, as well as make sure there are no unused shaders in the PSO cache.


# Export Settings 

Play tuşunun orada bulunan project settings kısmına basıp aşağıdaki ayarları yapıyoruz.

![Image](./Pics/Unreal-Engine-C--.__indir.png)

![Image](./Pics/Unreal-Engine-C--.__indir2.png)


# BP Save Graphic and Audio Settings
Save Game kısmında oluşturduğumuz değerlere ekstra olarak grafik ve ses kısmında bulunan tüm 
değerleri ekliyoruz. Bunları yaptıktan sonra widget’a dönüp Apply butonuna basıldıktan ve 
ayarları uyguladıktan sonra save’e değerlerin son hallerini aktarıyoruz.

![settings](./Pics/Unreal-Engine-C--._indir2.png)

Bu ayarları geri çıkıp girdiğimizde gitmemesi ve yazı olarak da yazması için aşağıdaki gibi event 
pre construct kısmına ekliyoruz.

![options](./Pics/Unreal-Engine-C--.__indir3.png)

# Audio Settings

Audio Settings için ayrı bir buton ve slider oluşturduktan sonra On Value Changed kısmını 
aşağıdaki gibi yaparak diğer kısımları gizleyip, ses ayarımızı uygulamış oluyoruz. Müzik vs. ayarı 
da aynı şekilde yapılıyor.

**Not:** Sesin tipinin ne olduğunu seçmeyi ve soundmix oluşturmayı unutmuyoruz.

![audio](./Pics/Unreal-Engine-C--.indir%20(1).png)

# Graphic Settings (with list)

List şeklinde grafik ayarları menüsü yapmak için ilk önce settings adında bir widget 
oluşturuyoruz. Ardından main menu kısmında önceden oluşturduğumuz butona bunu ekliyoruz 
ve bu sayede widgetlar arasında geçiş sağlıyoruz.

![button](./Pics/Unreal-Engine-C--.indir2.png)

Grafik ayarlarından geri dönmek için de aynı sistemi kullanıyoruz.

![resolution](./Pics/Unreal-Engine-C--._indir3.png)

Ekrana bu şekilde comboBox ekledikten sonra default options kısmına seçilebilecek çözünürlüğü 
giriyoruz ve ayarların en alt kısmında bulunan OnClicked’e tıklıyoruz.
ScreenResMap adında string değeri oluşturuyoruz ve onları bu şekilde ayarlıyoruz.(Map kısmının 
sağ tarafında bulunanlar karşılık geldiği kod)

![resVariable](./Pics/Unreal-Engine-C--._indir4.png)

Tüm grafik ayarlarını yaptıktan sonra onaylama görevi görmesi için Apply adında bir buton 
oluşturalım ve aynı şekilde tıklandığında çalışmasını sağlayalım.

![execute](./Pics/Unreal-Engine-C--._indir5.png)

# Graphic Settings (with button)

Yan taraflarında arttırma ve azaltma butonları bulunan grafik ayarları menüsü yapmak için ilk 
önce settings adında bir widget oluşturuyoruz. Ardından main menu kısmında önceden 
oluşturduğumuz butona bunu ekliyoruz ve bu sayede widgetlar arasında geçiş sağlıyoruz.

![Button](./Pics/Unreal-Engine-C--.indir3.png)

Grafik ayarlarından geri dönmek için de aynı sistemi kullanıyoruz.

![Graphics](./Pics/Unreal-Engine-C--.indir4.png)

Ekrana bu şekilde butonları ekledikten sonra sol kısma increase, sağ kısma decrease adını 
veriyoruz ve ayarların en alt kısmında bulunan OnClicked’e tıklıyoruz.
ScreenResMap ve ScreenResArray adında 2 adet string değeri oluşturuyoruz ve onları bu şekilde 
ayarlıyoruz.(Map kısmının sağ tarafında bulunanlar karşılık geldiği kod)

![Value](./Pics/Unreal-Engine-C--.indir5.png)

Space adında text değeri oluşturup bırakıyoruz bu sadece 3. değer sonrası boşluk gelmesini engelliyor. 

![settings](./Pics/Unreal-Engine-C--.indir6.png)

Decrase kısmı için ise sadece + 1 kısmını – 1 yapıyoruz.
Tüm grafik ayarlarını yaptıktan sonra onaylama görevi görmesi için Apply adında bir buton 
oluşturalım ve aynı şekilde tıklandığında çalışmasını sağlayalım.

![execute](./Pics/Unreal-Engine-C--.indir7.png)

Decrase kısmı için ise sadece + 1 kısmını – 1 yapıyoruz.
Tüm grafik ayarlarını yaptıktan sonra onaylama görevi görmesi için Apply adında bir buton 
oluşturalım ve aynı şekilde tıklandığında çalışmasını sağlayalım..

# Create Game Instance and Save

Game Intance blueprint olarak oluşturduktan sonra SaveSlotName string değeri ve 
SaveGameData obje referansı oluşturuyoruz bunun sonucunda bize eğer kayıt dosyası yoksa 
oluşturmayı eğer varsa onu yüklemeyi sağlıyor.

![Create Game Instance and Save](./Pics/Unreal-Engine-C--._indir%202.png)

# Open Next Level

Oyunu kazandın bilgisini verdikten sonra veya herhangi bir yer ile etkileşime girdikten sonra 
tetiklenecek blueprint şablonu.
Not: İlk önce levels adında string array oluşturuyoruz ardından şablonu uyguluyoruz ve otomatik 
olarak gelecek seviyeye bizi gönderiyor.

![Open Next Level](./Pics/Unreal-Engine-C--._indir.png)

# Draw Debug Line

Kütüphanesini ekledikten sonra attığımız oluşturduğumuz line trace’in nereye gittiğini görebiliriz.
```cpp
#include "DrawDebugHelpers.h"
DrawDebugLine(
GetWorld(), // world space
StartLocation, // start vector
EndLocation, // end vector
FColor(0, 255, 0), // color
false,
0.0f, // life time - unlimited
0, // priority
5.0f // thickeness
);
```

# Rotate Turret (2 Methods)

Rotate Turret
Sadece tek eksen ile döndürmek için diğer iki ekseni sabitliyoruz.
Not: LookAtTarget vektörü oyuncunun konumu veya başka bir nesnenin konumu olabilir.

**Method 1)**
```cpp
void APawnBase::RotateTurret(FVector LookAtTarget)
{
// Only can rotate left or right.
FVector LookAtTargetCleaned=FVector(
LookAtTarget.X,
LookAtTarget.Y,
TurretMesh->GetComponentLocation().Z
);
FVector StartLocation = TurretMesh->GetComponentLocation();
// Baktığımız yöne bakması için çeviriyoruz.
FRotator TurretRotation = FVector(LookAtTargetCleaned - StartLocation).Rotation();
TurretMesh->SetWorldRotation(TurretRotation);
}

```
**Method 2)**
```cpp
// Rotation Settings
void APawnBase::RotateTurret(FVector LookAtTarget)
{
 // Getting target info in child and calculating rotation value.
 FRotator RotationCalculate = 
 UKismetMathLibrary::FindLookAtRotation(TurretMesh->GetComponentLocation(), LookAtTarget);
 // Getting calculated value and applying 2 between value.
 FRotator TurretRotation = FMath::RInterpTo(TurretMesh->GetComponentRotation(), RotationCalculate,
 GetWorld()->GetDeltaSeconds(), RotateSpeed);
 // Setting turret rotation value
 TurretMesh->SetWorldRotation(TurretRotation);
}
```

# Health Box

İlk önce aktör oluşturuyoruz ardından detaylar kısmından verilecek can miktarını ve alındığında 
çıkacak olan sesi seçiyoruz.
- Alındığında can veren, silah veren veya diğer kutuların hepsi bu mantıkladır.

**HealthBox.h**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "HealthBox.generated.h"
UCLASS()
class CONTRA_API AHealthBox : public AActor
{
GENERATED_BODY()
public:
// Sets default values for this actor's properties
AHealthBox();
protected:
// Called when the game starts or when spawned
virtual void BeginPlay() override;
UFUNCTION()
void OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor, UPrimitiveComponent* OtherComponent,
FVector NormalImpulse, const FHitResult& Hit);
private:
void FindPointer();
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category= "Components",meta = (AllowPrivateAccess = true))
UStaticMeshComponent* HealthBoxMesh;
// Effect System
UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category= "Effects", meta = (AllowPrivateAccess = true))
UParticleSystem* GiveHealthParticle = nullptr;
UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category= "Effects", meta = (AllowPrivateAccess = true))
USoundBase* GiveHealthSound = nullptr;
// Healthbox giving this amounth health percent.
UPROPERTY(EditAnywhere, BlueprintReadOnly, Category= "Components",meta = (AllowPrivateAccess = true))
float GiveHealthValue = 10.0f;
};
```
**HealthBox.cpp**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "HealthBox.h"
#include "Components/StaticMeshComponent.h"
#include "ContraCharacter.h"
#include "Kismet/GameplayStatics.h"
// Sets default values
AHealthBox::AHealthBox()
{
// Set this actor to call Tick() every frame. You can turn this off to improve performan
ce if you don't need it.
PrimaryActorTick.bCanEverTick = false;
HealthBoxMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("HealthBox Mesh"));
HealthBoxMesh->OnComponentHit.AddDynamic(this, &AHealthBox::OnHit);
RootComponent = HealthBoxMesh;
}
// Called when the game starts or when spawned
void AHealthBox::BeginPlay()
{
Super::BeginPlay();
}
// When hit the player on healthbox, it will give 10.0f health value. After taking health, it
will destroy own.
void AHealthBox::OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor, UPrimitiveComponent* OtherComponent,
FVector NormalImpulse, const FHitResult& Hit)
{
AContraCharacter* ContraCharacter = Cast<AContraCharacter>(OtherActor);
if (!ContraCharacter) { return; }
if(ContraCharacter && OtherActor != this)
{
ContraCharacter->Health += GiveHealthValue;
FindPointer();
UGameplayStatics::SpawnEmitterAtLocation(this,GiveHealthParticle,GetActorLocation());
UGameplayStatics::SpawnSoundAtLocation(this, GiveHealthSound, GetActorLocation());
Destroy();
}
}
void AHealthBox::FindPointer()
{
if (!GiveHealthSound) { return; }
if (!GiveHealthParticle) { return; }
}
```

# Obstacle

Spawner v2’de istediğimiz nesneyi yaratmak için kullandığımız nesne olması için aktör 
oluşturuyoruz ve istediğimiz herhangi bir niagara efekti ekliyoruz.
- Karakterin engel arasında olup olmadığını anlamak için line trace kullandım.
- Yaratıldıktan istediğimiz kadar süre sonra yok olması için timer içerisinde DeadDelay 
kullandım.
- 2 engel arasına gelindiğinde karakterin canını azaltır.
- Görünüşü ve örnek kullanımı: 
[reddit](./Pics/www.reddit.com/r/unrealengine/comments/o97swk/random_obstacle_spawner_with_niagara_effect/)

**Obstacle.h**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#pragma once
#include "CoreMinimal.h"
#include "FireObstacleSpawner.h"
#include "GameFramework/Actor.h"
#include "NiagaraComponent.h"
#include "FireObstacle.generated.h"
class UActorMovement;
class AContraCharacter;
UCLASS()
class CONTRA_API AFireObstacle : public AActor
{
GENERATED_BODY()
public:
// Sets default values for this actor's properties
AFireObstacle();
virtual void Tick(float DeltaSeconds) override;
// Cast Spawner
UPROPERTY(VisibleDefaultsOnly, BlueprintReadOnly, Category = "Components")
AFireObstacleSpawner* FireObstacleSpawner = nullptr;
// Movement
UPROPERTY(EditAnywhere)
UActorMovement* ActorMovement = nullptr;
// Dead Time
UPROPERTY(EditAnywhere)
float DeadDelay = 10.0f;
private:
void ObstacleHit(); // When hit the obstacle
void EffectHitStart(); // when It spawned
void ObstacleDestroy();
// Main Settings
USceneComponent* SceneComponent = nullptr;
UPROPERTY(EditDefaultsOnly, Category= "Effect")
UStaticMeshComponent* EffectStart = nullptr; // Effect start location
UPROPERTY(EditDefaultsOnly, Category= "Effect")
UStaticMeshComponent* EffectEnd = nullptr; // Effect end location
// Niagara Effect
UPROPERTY(EditDefaultsOnly, Category= "Effect")
UNiagaraComponent* FireEffect = nullptr; // Effect type
FName EffectName = "User.BeamEnd"; // Effect name
// Cast Main Player
AContraCharacter* ContraCharacter = nullptr;
FTimerHandle DeadTimer;
protected:
// Called when the game starts or when spawned
virtual void BeginPlay() override;
};
```
**Obstacle.cpp**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "FireObstacle.h"
#include "Components/SceneComponent.h"
#include "Contra/ContraCharacter.h"
#include "Kismet/GameplayStatics.h"
#include "Contra/ActorMovement.h"
#include "Contra/FireObstacleSpawner.h"
#include "Engine/Public/TimerManager.h"
// Sets default values
AFireObstacle::AFireObstacle()
{
// Set this actor to call Tick() every frame. You can turn this off to improve performan
ce if you don't need it.
PrimaryActorTick.bCanEverTick = true;
SceneComponent = CreateDefaultSubobject<UStaticMeshComponent>("Obstacle Mesh");
RootComponent = SceneComponent;
EffectStart = CreateDefaultSubobject<UStaticMeshComponent>("Effect Start");
EffectStart->SetupAttachment(SceneComponent);
EffectEnd = CreateDefaultSubobject<UStaticMeshComponent>("Effect End");
EffectEnd->SetupAttachment(SceneComponent);
FireEffect = CreateDefaultSubobject<UNiagaraComponent>("Fire Effect");
FireEffect->SetupAttachment(EffectEnd);
// Actor Movement settings on details page.
ActorMovement = CreateDefaultSubobject<UActorMovement>("FireObstacle Movement");
}
// Called when the game starts or when spawned
void AFireObstacle::BeginPlay()
{
Super::BeginPlay();
// Calling main player.
ContraCharacter = Cast<AContraCharacter>(UGameplayStatics::GetPlayerCharacter(this, 0));
FireEffect->SetVectorParameter(EffectName, EffectStart->GetComponentLocation());
GetWorldTimerManager().SetTimer(DeadTimer, this, &AFireObstacle::ObstacleDestroy, 1.0f, false, DeadDelay);
}
void AFireObstacle::Tick(float DeltaSeconds)
{
EffectHitStart();
}
// It is creating LineTrace
void AFireObstacle::EffectHitStart()
{
// Setting effect location every frame because actor is moving.
FireEffect->SetVectorParameter(EffectName, EffectStart->GetComponentLocation());
FHitResult Hit;
FCollisionQueryParams TraceParams;
// sending line trace between end-start
GetWorld()->LineTraceSingleByObjectType(
OUT Hit,
EffectStart->GetComponentLocation(),
EffectEnd->GetComponentLocation(),
ECC_Pawn,
TraceParams
);
AActor* ActorHit = Hit.GetActor();
// when hit the pawn and if pawn tag is player, returning true.
if (ActorHit && ActorHit->ActorHasTag("Player"))
{
ObstacleHit();
}
}
// When hit the obstacle.
void AFireObstacle::ObstacleHit()
{
// if It called this function, decreasing health value.
if (ContraCharacter)
{
ContraCharacter->Health -= 0.5f;
}
}
void AFireObstacle::ObstacleDestroy()
{
GetWorldTimerManager().ClearTimer(DeadTimer);
Destroy();
}
```

# Spawner V2

Pawn oluşturduktan sonra istediğimiz yere spawner’ı sürükleyip bırakıyoruz. 
Detaylar kısmından istediğimiz örüntüyü oluşturuyoruz bu sayede sürekli 
olarak o örüntüyü devam ettiriyor.
Özellik: 
- Seçtiğiniz yönde(x,y veya z) ve belirlediğiniz aralıkta rastgele oluşturuyor. 
- Belirttiğiniz sıklıkta oluşturur. 
- Her üyenin farklı şekilde özelleştirilmesini sağlar. 
- Trigger'da iseniz belirlediğiniz sırayı takip ederek oluşturmaya devam eder. 
- Niagara efektine çarparsanız sağlığınızı düşürür. 
Yöntem: 
+ Her üyeyi özelleştirmek için structure kullandım. 
+ Yön seçmek için enum kullandım. 
+ Spawner'ı parent, oluşturulan nesneyi child olarak ayarladım, böylece iletişim kurmasını 
sağladım.
Örnek kullanımı ve görünüşü: 
[reddit](./Pics/www.reddit.com/r/unrealengine/comments/o97swk/random_obstacle_spawner_with_niagara_effect/)

**SpawnerV2.h**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#pragma once
#include "CoreMinimal.h"
#include "GameFramework/Pawn.h"
#include "Engine/TriggerVolume.h"
#include "FireObstacleSpawner.generated.h"
// Direction Choice
UENUM(BlueprintType)
enum ESpawnDirection
{
SpawnDirectionX UMETA(Display = "X direction"),
SpawnDirectionY UMETA(Display = "Y direction"),
SpawnDirectionZ UMETA(Display = "Z direction")
};
// Choice for every array elements.
USTRUCT(BlueprintType)
struct CONTRA_API FSpawnSettings
{
GENERATED_BODY()
public:
UPROPERTY(EditAnywhere)
TEnumAsByte<ESpawnDirection> SpawnDirection;
// Min-Max value for Spawn Location and Rotation
UPROPERTY(EditAnywhere)
float MinSpawnLocation;
UPROPERTY(EditAnywhere)
float MaxSpawnLocation;
// - or + Roll value
UPROPERTY(EditAnywhere)
float SpawnRotation;
};
class AContraCharacter;
UCLASS()
class CONTRA_API AFireObstacleSpawner : public APawn
{
GENERATED_BODY()
public:
// Sets default values for this actor's properties
AFireObstacleSpawner();
virtual void Tick(float DeltaSeconds) override;
protected:
// Called when the game starts or when spawned
virtual void BeginPlay() override;
virtual float TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent, AController
* EventInstigator,
AActor* DamageCauser) override;
private:
// Function
void ObstacleSpawn(); // creating actor
void SetSpawnTimer(); // calling timer
void SpawnChoice(); // defines values.
void BacktoNormal(); // Slow motion
bool IsDead() const;
// Spawner Main Settings
UPROPERTY(EditDefaultsOnly)
UStaticMeshComponent* SpawnerMesh = nullptr;
// Cast Main Player
AContraCharacter* ContraCharacter = nullptr;
// Spawn Object
UPROPERTY(EditAnywhere, Category = "Main Settings")
TSubclassOf<AActor> ActorToSpawn;
FTimerHandle SpawnTimer;
// Info from struct
UPROPERTY(EditAnywhere, meta = (TitleProperty = "Spawner Settings"))
TArray<struct FSpawnSettings> SpawnSetting;
// Spawner temp value for set the actor.
FVector SpawnerLocation;
FRotator SpawnerRotation;
// Trigger Volume for the ActorMovement
UPROPERTY(EditAnywhere, Category = "Spawner Settings")
ATriggerVolume* Trigger = nullptr;
// Spawner Settings
UPROPERTY(EditAnywhere, Category = "Spawner Settings")
float SpawnSpeed = 0.5f;
UPROPERTY(EditAnywhere, Category = "Spawner Settings")
float SpawnRepeat = 1.0f;
UPROPERTY(EditAnywhere, Category = "Spawner Settings")
float SpawnedMovementSpeed = 300.0f;
UPROPERTY(EditAnywhere, Category = "Spawner Settings")
float SpawnedDeadTime = 10.0f; // Spawned obstacle destroy time
int i = 0; // array start value
bool IsWork = true;
// Slow Motion
bool SlowLogic = false;
FTimerHandle BacktoNormalTimer;
float TimerValue = 0.01;
// Health
bool ReturnGameModeDead = true;
UPROPERTY(EditDefaultsOnly)
float MaxHealth = 100.0f;
UPROPERTY(VisibleAnywhere)
float Health;
// Effect Choice
UPROPERTY(EditAnywhere, Category = "Effects", meta = (AllowPrivateAccess = "true"))
UParticleSystem* DeathParticle = nullptr;
UPROPERTY(EditAnywhere, Category = "Effects")
USoundBase* DeathSound;
// for detect attached actor or static meshes
TArray<AActor*> Attached;
UPROPERTY(EditAnywhere)
bool DestroyWAttachment = false;
};
```
**SpawnverV2.cpp**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "FireObstacleSpawner.h"
#include "Contra/ContraCharacter.h"
#include "FireObstacle.h"
#include "ActorMovement.h"
#include "ContraGameMode.h"
#include "Engine/World.h"
#include "TimerManager.h"
#include "Kismet/GameplayStatics.h"
// Sets default values
AFireObstacleSpawner::AFireObstacleSpawner()
{
// Set this actor to call Tick() every frame. You can turn this off to improve performan
ce if you don't need it.
PrimaryActorTick.bCanEverTick = true;
SpawnerMesh = CreateDefaultSubobject<UStaticMeshComponent>("Spawner Mesh");
RootComponent = SpawnerMesh;
}
// Called when the game starts or when spawned
void AFireObstacleSpawner::BeginPlay()
{
Super::BeginPlay();
// Calling main player.
ContraCharacter = Cast<AContraCharacter>(UGameplayStatics::GetPlayerCharacter(this, 0));
Health = MaxHealth;
}
void AFireObstacleSpawner::Tick(float DeltaSeconds)
{
 // if player goes out of the trigger, timer will stop (spawnner).
if (Trigger && Trigger->IsOverlappingActor(ContraCharacter) && IsWork)
{
SetSpawnTimer();
IsWork = false;
}
else if (Trigger && !Trigger->IsOverlappingActor(ContraCharacter))
{
GetWorldTimerManager().PauseTimer(SpawnTimer);
IsWork = true;
}
}
// Call Function with Timer
void AFireObstacleSpawner::SetSpawnTimer()
{
GetWorldTimerManager().UnPauseTimer(SpawnTimer);
GetWorldTimerManager().SetTimer(SpawnTimer, this, &AFireObstacleSpawner::ObstacleSpawn, S
pawnRepeat, true,
SpawnSpeed);
}
// Spawn Actor
void AFireObstacleSpawner::ObstacleSpawn()
{
// Repeating array 0 to max and restarting.
if (i < SpawnSetting.Num())
{
SpawnChoice();
i++;
}
else
{
i = 0;
SpawnChoice();
}
// It's giving information to child (SpawnedActor)
AFireObstacle* SpawnedActor = GetWorld()->SpawnActor<AFireObstacle>(ActorToSpawn, Spawner
Location, SpawnerRotation);
if (SpawnedActor && SpawnedActor->ActorMovement && Trigger)
{
SpawnedActor->DeadDelay = SpawnedDeadTime;
SpawnedActor->ActorMovement->Direction = DirectionX;
SpawnedActor->ActorMovement->PressPlate = Trigger;
SpawnedActor->ActorMovement->TransporterForwardSpeed = SpawnedMovementSpeed;
SpawnedActor->FireObstacleSpawner = this;
}
}
// Set Properties
void AFireObstacleSpawner::SpawnChoice()
{
// It's giving random value to each array elements and repeats every time we call this fu
nction.
switch (SpawnSetting[i].SpawnDirection)
{
case SpawnDirectionZ:
{
SpawnerLocation = GetActorLocation();
SpawnerLocation.Z = GetActorLocation().Z - FMath::RandRange(
SpawnSetting[i].MinSpawnLocation,
SpawnSetting[i].MaxSpawnLocation);
SpawnerRotation = FRotator(0, 0, SpawnSetting[i].SpawnRotation);
break;
}
case SpawnDirectionY:
{
SpawnerLocation = GetActorLocation();
SpawnerLocation.Y = GetActorLocation().Y - FMath::RandRange(
SpawnSetting[i].MinSpawnLocation,
SpawnSetting[i].MaxSpawnLocation);
SpawnerRotation = FRotator(0, 0, SpawnSetting[i].SpawnRotation);
break;
}
case SpawnDirectionX:
{
SpawnerLocation = GetActorLocation();
SpawnerLocation.X = GetActorLocation().X - FMath::RandRange(
SpawnSetting[i].MinSpawnLocation,
SpawnSetting[i].MaxSpawnLocation);
SpawnerRotation = FRotator(0, 0, SpawnSetting[i].SpawnRotation);
break;
}
}
}
float AFireObstacleSpawner::TakeDamage(float DamageAmount, FDamageEvent const& DamageEvent,
AController* EventInstigator, AActor* DamageCauser)
{
// pawn taking damage and applying health.
float DamageToApply = Super::TakeDamage(DamageAmount, DamageEvent, EventInstigator, Damag
eCauser);
DamageToApply = FMath::Min(Health, DamageToApply);
Health -= DamageToApply;
// when health was less than 10, it will set start slow motion value and call function.
if (Health < 10)
{
SlowLogic = true;
UGameplayStatics::SetGlobalTimeDilation(GetWorld(), TimerValue);
BacktoNormal();
}
// when player was dead, it will spawn particle and sound.
if (IsDead())
{
UGameplayStatics::SpawnEmitterAtLocation(this, DeathParticle, GetActorLocation());
UGameplayStatics::SpawnSoundAtLocation(this, DeathSound, GetActorLocation());
// pawn is giving information to gamemode.
AContraGameMode* GameMode = GetWorld()->GetAuthGameMode<AContraGameMode>();
if (GameMode != nullptr && ReturnGameModeDead)
{
GameMode->PawnKilled(this);
// returning game mode one! It's protecting sum more score.
ReturnGameModeDead = false;
}
if (Controller != nullptr && Controller->GetPawn() == this)
{
Controller->PawnPendingDestroy(this);
if (Controller != nullptr)
{
Controller->UnPossess();
Controller = nullptr;
}
}
//Destroy();
// GetCapsuleComponent()->SetCollisionEnabled(ECollisionEnabled::NoCollision);
}
return DamageToApply;
}
// Check Health less than 0
bool AFireObstacleSpawner::IsDead() const
{
return Health <= 0;
}
// Slow Motion with Destroy
void AFireObstacleSpawner::BacktoNormal()
{
// setting timer slow motion
GetWorldTimerManager().SetTimer(BacktoNormalTimer, this, &AFireObstacleSpawner::BacktoNor
mal, 2, true, 0.1f);
if (SlowLogic)
{
// when player was dead and slow time is enough, it will back to the normal and destr
oy himself.
if (TimerValue >= 0.5f && Health <= 0)
{
TimerValue = 1.0f;
UGameplayStatics::SetGlobalTimeDilation(this, TimerValue);
GetWorldTimerManager().ClearTimer(BacktoNormalTimer);
GetWorldTimerManager().ClearTimer(SpawnTimer);
// Detect attached actors or static meshes and it will destroy with herself
if (DestroyWAttachment)
{
GetAttachedActors(Attached);
for(int a = 0; a < Attached.Num(); a++)
{
Attached[a]->Destroy();
}
}
Destroy();
}
else
{
// if player doesn't dead and slow motion enough, slow time returning normal but
slowly.
TimerValue += 0.1;
UGameplayStatics::SetGlobalTimeDilation(this, TimerValue);
}
}
}
```

# Gun

İlk önce actor oluşturuyoruz ve bunu ana karakterimize ekliyoruz.

**Gun.h**
```cpp
public:
void PullTrigger();
UPROPERTY(EditAnywhere, Category = "Attack", meta = (AllowPrivateAccess = "true"))
int DefaultAmmo;
int Ammo;
UPROPERTY(EditAnywhere, Category = "Attack", meta = (AllowPrivateAccess = "true"))
float FireRate = 0.5f;
UPROPERTY(EditAnywhere, Category = "Attack", meta = (AllowPrivateAccess = "true"))
float FireRepeat = 1.0f;
private:
UPROPERTY(VisibleAnywhere)
USceneComponent* Root;
UPROPERTY(VisibleAnywhere)
USkeletalMeshComponent* Mesh;
UPROPERTY(EditAnywhere, Category = "Effect", meta = (AllowPrivateAccess = "true"))
UParticleSystem* MuzzleFlash;
UPROPERTY(EditAnywhere, Category = "Effect", meta = (AllowPrivateAccess = "true"))
UParticleSystem* ImpactEffect;
UPROPERTY(EditAnywhere, Category = "Effect", meta = (AllowPrivateAccess = "true"))
USoundBase* MuzzleSound;
UPROPERTY(EditAnywhere, Category = "Effect", meta = (AllowPrivateAccess = "true"))
USoundBase* ImpactSound;
UPROPERTY(EditAnywhere, Category = "Attack", meta = (AllowPrivateAccess = "true"))
float MaxRange = 1000;
UPROPERTY(EditAnywhere)
float Damage = 10;
bool GunTrace(FHitResult &Hit, FVector &ShotDirection);
AController* GetOwnerController() const;
```
**Gun.cpp**
```cpp
// Fill out your copyright notice in the Description page of Project Settings.
#include "Gun.h"
#include "Components/SkeletalMeshComponent.h"
#include "Kismet/GameplayStatics.h"
#include "DrawDebugHelpers.h"
// Sets default values
AGun::AGun()
{
// Set this actor to call Tick() every frame. You can turn this off to improve performan
ce if you don't need it.
PrimaryActorTick.bCanEverTick = true;
Root = CreateDefaultSubobject<USceneComponent>(TEXT("Root"));
SetRootComponent(Root);
Mesh = CreateDefaultSubobject<USkeletalMeshComponent>(TEXT("Mesh"));
Mesh->SetupAttachment(Root);
}
void AGun::PullTrigger()
{
// Effect Spawn
UGameplayStatics::SpawnEmitterAttached(MuzzleFlash, Mesh, TEXT("MuzzleFlashSocket"));
UGameplayStatics::SpawnSoundAttached(MuzzleSound, Mesh, TEXT("MuzzleFlashSocket"));
FHitResult Hit;
FVector ShotDirection;
bool bSuccess = GunTrace(Hit, ShotDirection);
// if distance possible create particle and LineTraceSingle at location
if (bSuccess)
{
UGameplayStatics::SpawnEmitterAtLocation(
GetWorld(),
ImpactEffect,
Hit.Location,
ShotDirection.Rotation());
UGameplayStatics::SpawnSoundAtLocation(
GetWorld(),
ImpactSound,
Hit.Location,
ShotDirection.Rotation()
);
AActor* HitActor = Hit.GetActor();
if (HitActor != nullptr)
{
FPointDamageEvent DamageEvent(Damage, Hit, ShotDirection, nullptr);
AController* OwnerController = GetOwnerController();
HitActor->TakeDamage(Damage, DamageEvent, OwnerController, this);
}
}
}
bool AGun::GunTrace(FHitResult& Hit, FVector& ShotDirection)
{
AController* OwnerController = GetOwnerController();
if (!OwnerController) { return false; }
FVector Location;
FRotator Rotation;
OwnerController->GetPlayerViewPoint(Location, Rotation);
ShotDirection = -Rotation.Vector();
// We are calculating PlayerViewPoint between Wall distance
FVector End = Location + Rotation.Vector() * MaxRange;
// We are ignoring own character
FCollisionQueryParams Params;
Params.AddIgnoredActor(this);
Params.AddIgnoredActor(GetOwner());
return GetWorld()->LineTraceSingleByChannel(
Hit, Location,
End,
ECollisionChannel::ECC_GameTraceChannel1,
Params);
}
AController* AGun::GetOwnerController() const
{
APawn* OwnerPawn = Cast<APawn>(GetOwner());
if (OwnerPawn == nullptr) { return nullptr; }
return OwnerPawn->GetController();
}
```

# Projectile

Projecttile için ilk önce actor oluşturuyoruz. Aşşağıdaki gibi project tile oluşturduktan sonra bunu 
silaha veya karaktere ekliyoruz.

**ProjecttileBase.h**
```cpp
class UProjectileMovementComponent;
private:
// COMPONENTS
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components", meta = (AllowPrivateAccess = "true"))
UProjectileMovementComponent* ProjectileMovement = nullptr;
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Move", meta = (AllowPrivateAccess = "true"))
float MovementSpeed = 1300.0f;
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components", meta = (AllowPrivateAccess = "true"))
UStaticMeshComponent* ProjectileMesh = nullptr;
UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Components", meta = (AllowPrivateAccess = "true"))
UParticleSystemComponent* ParticleTrail = nullptr;
// VARIABLES
UPROPERTY(EditDefaultsOnly, Category = "Damage")
TSubclassOf<UDamageType> DamageType;
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Damage", meta = (AllowPrivateAccess = "true"))
float Damage = 50.0f;
// Projecttile effect(behind smoke)
UPROPERTY(EditAnywhere, Category= "Effect")
UParticleSystem* HitParticle = nullptr;
// Audio
UPROPERTY(EditAnywhere, Category = "Effect")
USoundBase* HitSound;
UPROPERTY(EditAnywhere, Category = "Effect")
USoundBase* LaunchSound;
UPROPERTY(EditAnywhere, Category = "Effects")
TSubclassOf<UCameraShake> HitShake;
// FUNCTION
UFUNCTION()
void OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor, UPrimitiveComponent* Ot
herComponent, FVector NormalImpulse, const FHitResult& Hit);
```
**Projecttile.cpp**
```cpp
#include "ProjectileBase.h"
#include "Components/StaticMeshComponent.h"
#include "GameFramework/ProjectileMovementComponent.h"
#include "Kismet/GameplayStatics.h"
#include "Particles/ParticleSystemComponent.h"
// Sets default values
AProjectileBase::AProjectileBase()
{
PrimaryActorTick.bCanEverTick = false;
ProjectileMesh = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Projectile Mesh"));
ProjectileMesh->OnComponentHit.AddDynamic(this, &AProjectileBase::OnHit);
RootComponent = ProjectileMesh;
ProjectileMovement = CreateDefaultSubobject<UProjectileMovementComponent>(TEXT("Projectil
e Movement"));
ProjectileMovement->InitialSpeed = MovementSpeed;
ProjectileMovement->MaxSpeed = MovementSpeed;
ParticleTrail = CreateDefaultSubobject<UParticleSystemComponent>(TEXT("Particle Trail"));
ParticleTrail->SetupAttachment(RootComponent);
InitialLifeSpan = 3.0f;
}
void AProjectileBase::BeginPlay()
{
Super::BeginPlay();
UGameplayStatics::PlaySoundAtLocation(this,LaunchSound,GetActorLocation());
}
void AProjectileBase::OnHit(UPrimitiveComponent* HitComponent, AActor* OtherActor, UPrimitive
Component* OtherComponent, FVector NormalImpulse, const FHitResult& Hit)
{
AActor* MyOwner = GetOwner();
if (!MyOwner) { return; }
if (OtherActor && OtherActor != this && OtherActor != MyOwner)
{
UGameplayStatics::ApplyDamage(
OtherActor,
Damage,
MyOwner->GetInstigatorController(),
this,
DamageType
);
// When hitted object, It will create hitparticle and audio.
UGameplayStatics::SpawnEmitterAtLocation(this, HitParticle, GetActorLocation());
UGameplayStatics::PlaySoundAtLocation(this, HitSound, GetActorLocation());
GetWorld()->GetFirstPlayerController()->ClientPlayCameraShake(HitShake);
Destroy();
}
}
```

# Spawner

Spawner adında bir actor oluşturuyoruz.

**Spawner.h:**
```cpp
private:
 void Spawn();
 void CalculateSpawn();
 FTimerHandle SpawnTimer;
 UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Spawn Settings", meta = (AllowPrivateAccess = "true"))
 FVector SpawnerLocation;
 UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Spawn Settings", meta = (AllowPrivateAccess = "true"))
 FRotator SpawnerRotation;
 // Spawn Object
 UPROPERTY(EditAnywhere, Category = "Spawner Settings")
 TSubclassOf<AActor> ActorToSpawn;
 // Spawner Settings
 UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Spawner Settings", meta = (AllowPrivateAccess = "true"))
 float SpawnSpeed = 0.5f;
 UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Spawner Settings", meta = (AllowPrivateAccess = "true"))
 float SpawnRepeat = 1.0f;
 // Spawn Location and Rotation
 UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Spawner Settings", meta = 
(AllowPrivateAccess = "true"))
 float SpawnMinZ = -30.0f;
 UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Spawner Settings", meta = (AllowPrivateAccess = "true"))
 float SpawnMaxZ = 300.0f;
```
**Spawner.cpp**
```cpp
#include "Engine/World.h"
#include "GameFramework/Actor.h"
#include "TimerManager.h"
void ASpawner::BeginPlay()
{
 Super::BeginPlay();
 // Settings for spawn time etc.
 GetWorldTimerManager().SetTimer(SpawnTimer, this, &ASpawner::Spawn, SpawnRepeat, true, 
SpawnSpeed);
}
void ASpawner::Spawn()
{
 CalculateSpawn();
 /*
 if you want to spawn other cpp file and reach value use this:
 APipeActor* SpawnedActor = GetWorld()->SpawnActor<APipeActor>(ActorToSpawn, 
SpawnerLocation, SpawnerRotation);
 if (SpawnedActor)
 {
 SpawnedActor->Spawner = this;
 }
 */
 GetWorld()->SpawnActor<AActor>(ActorToSpawn, SpawnerLocation, SpawnerRotation);
}
void ASpawner::CalculateSpawn()
{
 // it is spawning between minZ - maxZ value.
 UE_LOG(LogTemp, Warning, TEXT("Spawned"));
 SpawnerLocation = GetActorLocation();
 SpawnerLocation.Z = GetActorLocation().Z - FMath::RandRange(SpawnMinZ, SpawnMaxZ);
 SpawnerRotation = GetActorRotation();
}
```

# Movable Platform

İlk önce hangi nesnenin hareket edeceğini belirlemek için actor component oluşturup onu neseneye 
ekliyoruz. Hemen ardından nesnenin parenti olacak trigger volume ekliyoruz eğer platform harekete 
başladığında ses gelmesini istiyorsak, nesnenin detaylar panelinden add component diyerek audio 
component ekliyoruz. Audio component de detaylar kısmından auto active özelliğini kapatıyoruz (oyun 
başlar başlamaz çalmaması için).

**ActorMovement.h:**
```cpp
#include "Components/ActorComponent.h"
#include "Engine/TriggerVolume.h"
UENUM()
enum EPlatformDirection
{
 DirectionX UMETA(Display = "X direction"),
 DirectionY UMETA(Display = "Y direction"),
 DirectionZ UMETA(Display = "Z direction")
};
class CONTRA_API UActorMovement : public UActorComponent
{
 GENERATED_BODY()
private:
 UPROPERTY(EditAnywhere)
 ATriggerVolume* PressPlate = nullptr;
 UPROPERTY(EditAnywhere)
 UAudioComponent* AudioComponent = nullptr;
 UPROPERTY(EditAnywhere)
 AActor* ActorThatOpen;
 UPROPERTY(EditAnywhere, BlueprintReadOnly, Category= "Direction", meta = (AllowPrivateAccess = "true"))
 TEnumAsByte<EPlatformDirection> Direction;
 float DirectionValue;
 FVector Backward;
 FVector Forward;
 float TransporterForwardLast = 0.0f;
 UPROPERTY(EditAnywhere)
 float TransporterForwardSpeed = 200.0f;
 UPROPERTY(EditAnywhere)
 float TransportDelay = 5.0f;
 float TransporterInitial;
 float TransporterCurrent;
 UPROPERTY(EditAnywhere)
 float TransporterTarget = 3000;
public:
 // Sets default values for this component's properties
 UActorMovement();
 // Called every frame
 virtual void TickComponent(float DeltaTime, ELevelTick TickType,
 FActorComponentTickFunction* ThisTickFunction) override;
 void FindPressPlate();
 void FindAudioComponent();
 void DirectionChoice();
 void ForwardTransporter(float DeltaTime);
 void BackwardTransporter(float DeltaTime);
 void TransporterLogic(float DeltaTime);
 bool ForwardSound = false;
 bool BackwardSound = true;
```

**Transfer.cpp:**

```cpp
#include "ActorMovement.h"
#include "Components/AudioComponent.h"
#include "Engine/World.h"
#include "GameFramework/Actor.h"
#include "GameFramework/PlayerController.h"
#define OUT
// Called when the game starts
void UActorMovement::BeginPlay()
{
 Super::BeginPlay();
 // Getting your direction choice.
 DirectionChoice();
 // Setting initial value for target.
 TransporterInitial = DirectionValue;
 TransporterCurrent = TransporterInitial;
 TransporterTarget = TransporterInitial + TransporterTarget;
 // for the null pointer
 FindPressPlate();
 FindAudioComponent();
 // it is answering to "who can start this movement ?"
 ActorThatOpen = GetWorld()->GetFirstPlayerController()->GetPawn();
}
// Called every frame
void UActorMovement::TickComponent(float DeltaTime, ELevelTick TickType, 
FActorComponentTickFunction* ThisTickFunction)
{
 Super::TickComponent(DeltaTime, TickType, ThisTickFunction);
 TransporterLogic(DeltaTime);
}
void UActorMovement::DirectionChoice()
{
 // Getting your choice and setting value for target.
 switch (Direction)
 {
 case DirectionX:
 {
 DirectionValue = GetOwner()->GetActorLocation().X;
 //Forward
 Forward = GetOwner()->GetActorLocation();
 Forward.X = TransporterCurrent;
 //Backward
 Backward = GetOwner()->GetActorLocation();
 Backward.X = TransporterCurrent;
 break;
 }
 case DirectionY:
 {
 DirectionValue = GetOwner()->GetActorLocation().Y;
 //Forward
 Forward = GetOwner()->GetActorLocation();
 Forward.Y = TransporterCurrent;
 //Backward
 Backward = GetOwner()->GetActorLocation();
 Backward.Y = TransporterCurrent;
 break;
 }
 case DirectionZ:
 {
 DirectionValue = GetOwner()->GetActorLocation().Z;
 //Forward
 Forward = GetOwner()->GetActorLocation();
 Forward.Z = TransporterCurrent;
 //Backward
 Backward = GetOwner()->GetActorLocation();
 Backward.Z = TransporterCurrent;
 break;
 }
 default:
 DirectionValue = GetOwner()->GetActorLocation().Z;
 //Forward
 Forward = GetOwner()->GetActorLocation();
 Forward.Z = TransporterCurrent;
 //Backward
 Backward = GetOwner()->GetActorLocation();
 Backward.Z = TransporterCurrent;
 break;
 }
}
void UActorMovement::TransporterLogic(float DeltaTime)
{
 if (PressPlate && PressPlate->IsOverlappingActor(ActorThatOpen))
 {
 if (TransporterCurrent != TransporterTarget)
 {
 // when it was moving, it is updating location.
 ForwardTransporter(DeltaTime);
 // it is updating last value to return.
 TransporterForwardLast = GetWorld()->GetTimeSeconds();
 }
 }
 else if (PressPlate && !PressPlate->IsOverlappingActor(ActorThatOpen))
 {
 // returns after delay
 if (GetWorld()->GetTimeSeconds() - TransporterForwardLast > TransportDelay)
 {
 BackwardTransporter(DeltaTime);
 }
 }
}
void UActorMovement::ForwardTransporter(float DeltaTime)
{
 // Calculating movement speed with direction.
 TransporterCurrent = FMath::FInterpConstantTo(TransporterCurrent, TransporterTarget, 
DeltaTime,
 TransporterForwardSpeed);
 DirectionChoice();
 GetOwner()->SetActorLocation(Forward);
 BackwardSound = false;
 // to play sound while moving
 if (!AudioComponent) { return; }
 if (!ForwardSound)
 {
 AudioComponent->Play();
 ForwardSound = true;
 }
}
void UActorMovement::BackwardTransporter(float DeltaTime)
{
 // Calculating movement speed with direction.
 TransporterCurrent = FMath::FInterpConstantTo(TransporterCurrent, TransporterInitial,
 DeltaTime,
TransporterForwardSpeed);
 DirectionChoice();
 GetOwner()->SetActorLocation(Backward);
 // to play sound while moving
 ForwardSound = false;
 if (!AudioComponent) { return; }
 if (!BackwardSound)
 {
 AudioComponent->Play();
 BackwardSound = true;
 }
}
void UActorMovement::FindAudioComponent()
{
 // it is picking to audio.
 // Note: Don't forget add audio component on details.
 AudioComponent = GetOwner()->FindComponentByClass<UAudioComponent>();
 // for the null pointer.
 if (!AudioComponent)
 {
 UE_LOG(LogTemp, Warning, TEXT("%s Audio Component bulunamadı"), 
*GetOwner()->GetName());
 }
}
void UActorMovement::FindPressPlate()
{
 // for the null pointer.
 if (!PressPlate)
 {
 UE_LOG(LogTemp, Warning, TEXT("PressPlate not found!!"));
 }
}
}
```

# Grab

Scene Component oluşturuyoruz ve eğer bir objenin taşınabilir olmasını istiyorsak objeyi 
moveable, simulate physics true olarak işaretliyoruz.

**Grabber.h:**

```cpp
#include "CoreMinimal.h"
#include "FirstPersonCharacter.h"
#include "Components/SceneComponent.h"
#include "PhysicsEngine/PhysicsHandleComponent.h"
#include "Grabber.generated.h"
public:
 // Sets default values for this component's properties
 UGrabber();
 // Called every frame
 virtual void TickComponent(float DeltaTime, ELevelTick TickType,
 FActorComponentTickFunction* ThisTickFunction) override;
 void Grab();
 void Release();
private:
 void Grabbed();
 FVector GetMaxGrabLocation() const;
 FVector GetHoldLocation() const;
 UFUNCTION(BlueprintCallable, BlueprintPure)
 UPhysicsHandleComponent* GetPhysicsComponent() const;
 FHitResult GetFirstPhysicsBodyInReach() const;
 UPROPERTY(EditAnywhere, BlueprintReadOnly, meta = (AllowPrivateAccess = "true"))
 float MaxGrabDistance = 100;
 UPROPERTY(EditAnywhere, BlueprintReadOnly, meta = (AllowPrivateAccess = "true"))
 float HoldDistance = 100;
protected:
 // Called when the game starts
 virtual void BeginPlay() override;
 UFUNCTION(BlueprintCallable, BlueprintImplementableEvent)
 void NotifyQuestActor(AActor* Actor);
 AFirstPersonCharacter* FirstPersonCharacter = nullptr;
```

**Grabber.cpp:**

```cpp
#include "Grabber.h"
#include "FirstPersonCharacter.h"
#include "Engine/World.h"
#include "GameFramework/Actor.h"
#define OUT
// Sets default values for this component's properties
UGrabber::UGrabber()
{
 // Set this component to be initialized when the game starts, and to be ticked every frame. 
You can turn these features
 // off to improve performance if you don't need them.
 PrimaryComponentTick.bCanEverTick = true;
}
// Called when the game starts
void UGrabber::BeginPlay()
{
 Super::BeginPlay();
 FirstPersonCharacter = Cast<AFirstPersonCharacter>(GetOwner());
 if (!FirstPersonCharacter) { return; }
 FirstPersonCharacter->GetGrabber = this;
 GetPhysicsComponent();
}
// Called every frame
void UGrabber::TickComponent(float DeltaTime, ELevelTick TickType, 
FActorComponentTickFunction* ThisTickFunction)
{
 Super::TickComponent(DeltaTime, TickType, ThisTickFunction);
 Grabbed();
}
FVector UGrabber::GetMaxGrabLocation() const
{
 FVector PlayerViewPointLocation;
 FRotator PlayerViewPointRotation;
 GetWorld()->GetFirstPlayerController()->GetPlayerViewPoint(
 OUT PlayerViewPointLocation,
 OUT PlayerViewPointRotation
 );
 return PlayerViewPointLocation + PlayerViewPointRotation.Vector() * MaxGrabDistance;
}
FVector UGrabber::GetHoldLocation() const
{
 FVector PlayerViewPointLocation;
 FRotator PlayerViewPointRotation;
 GetWorld()->GetFirstPlayerController()->GetPlayerViewPoint(
 OUT PlayerViewPointLocation,
 OUT PlayerViewPointRotation
 );
 return PlayerViewPointLocation + PlayerViewPointRotation.Vector() * HoldDistance;
}
UPhysicsHandleComponent* UGrabber::GetPhysicsComponent() const
{
 return GetOwner()->FindComponentByClass<UPhysicsHandleComponent>();
}
void UGrabber::Grab()
{
 FHitResult HitResult = GetFirstPhysicsBodyInReach();
 AActor* HitActor = HitResult.GetActor();
 UPrimitiveComponent* HitComponent = HitResult.GetComponent();
 if (HitActor && HitComponent)
 {
 if (!GetPhysicsComponent()) { return; }
 //HitComponent->SetSimulatePhysics(true);
 GetPhysicsComponent()->GrabComponentAtLocationWithRotation(
 HitComponent,
 NAME_None,
 HitComponent->GetCenterOfMass(),
 FRotator()
 );
 NotifyQuestActor(HitActor);
 }
}
void UGrabber::Grabbed()
{
 if (!GetPhysicsComponent()) { return; }
 if (GetPhysicsComponent()->GrabbedComponent)
 {
 GetPhysicsComponent()->SetTargetLocation(GetMaxGrabLocation());
 }
}
void UGrabber::Release()
{
 if (!GetPhysicsComponent()) { return; }
 GetPhysicsComponent()->ReleaseComponent();
}
FHitResult UGrabber::GetFirstPhysicsBodyInReach() const
{
 FHitResult Hit;
 FCollisionQueryParams TraceParams(FName(TEXT("")), false, GetOwner());
 GetWorld()->LineTraceSingleByObjectType(
 OUT Hit,
 GetHoldLocation(),
 GetMaxGrabLocation(),
 FCollisionObjectQueryParams(ECollisionChannel::ECC_PhysicsBody),
 TraceParams
 );
 return Hit;
}
```

# BP Create Collectable Object

<h3>1) Create UniqueID</h3>

![Image](./Pics/Unreal-Engine-C--.image56.png)

<h3>2) Return UniqueID</h3>

![Image](./Pics/Unreal-Engine-C--.image53.png)

<h3>3) Save Collectable UniqueID (when you hit the object)</h3>

![Image](./Pics/Unreal-Engine-C--.image55.png)

<h3>4) Get UniqueID</h3>

![Image](./Pics/Unreal-Engine-C--.image41.png)

<h3> 5) Save Collected Object</h3>

![Image](./Pics/Unreal-Engine-C--.image58.png)

<h3> 6) Load Collected Object</h3>

![Image](./Pics/Unreal-Engine-C--.__image59.png)

<h3>7) Level Blueprint Change State</h3>

![Image](./Pics/Unreal-Engine-C--.image61.png)

# BP Set Game Options

![Image](./Pics/Unreal-Engine-C--.image51.png)

# BP Load Options

![Image](./Pics/Unreal-Engine-C--._image52.png)

# BP Load Save

![Image](./Pics/Unreal-Engine-C--.image49.png)

# BP Create Game Options

![Image](./Pics/Unreal-Engine-C--.image50.png)

# BP Create Save

![Image](./Pics/Unreal-Engine-C--.image47.png)

# Asset Manager

Oyun içinde itemleri, iksirleri vb. itemleri tutmak için c++ **FPrimaryAssetType** ile **asset manager** oluşturup **defaultEngine.ini** dosyasına **AssetManagerClassName = /Script/GameName.DosyaIsmiAssetManager** şeklinde ekleme yapıyoruz. 

Artık **Project Settings -> Asset Manager** kısmında eklediğimizi görebiliriz. Oyun içinde bunları kullanmak için Bluepint kısmında tüm itemleri for each loop döngüsü ile alıp Get Primary Id List ile isimleri eşleşiyormu diye kontrol ederiz ve çıktıları async load primary asset list ile tekrar for each loop’a alıp ona göre stored items listesine ekleyip tutarız. Bunun sayesinde tüm itemlerimizi bilmiş oluruz.

# Gameplay Ability Plugin

Oyun içi mana, can veya güç veren iksir, yetenekleri yönetmek için gameplay ability plugin kullanıyoruz. Buna herhangi bir iksir vs. eklemek için sadece data asset oluşturmak yeterli.

# Server Type Check

```cpp
// check if is true, it's server. Else, it's client
if (HasAuthority())
{
FVector Location = GetActorLocation();
Location += FVector(5 * DeltaTime, 0, 0);
SetActorLocation(Location);
}

```

# Game Start with CMD

Unreal Engine dosyasında engine/binaries içerisinde **UE4Editor.Exe** adresi ve projenin .uproject
doyasının adresinin arasına boşluk koyup **-game** dediğimiz zaman konsola direkt oyunu başlatıyor. **“-
game”** sonrası **-log** koyup başlatırsak oyun oynanırken olan tüm işlemleri gösteriyor.
Farklı bir map ile başlatmak için **/Game/Oyunİsmi/HaritaDosyası/Mapİsmi** olarak **-game** öncesine yazıp
başlatıyoruz.

Multiplayer server’e bağlanmak için **-game** yerine **-server** yazıp başlatıyoruz.
Multiplayer oyunu başlatmak için **local ip adresini** yazip daha sonrasında **-game** yazarak başlatıyoruz.
(eğer 2 kere aynı komutu girersek aynı dünyaya bir oyuncuyu daha gönderir)
Bunun sayesinde crash vs. yediğimizde oyunun nerede hata verdiğiniz bulup düzeltebiliyoruz.


# Steam Lobby System C++ (GameDevTV Course)

Multiplayer steam sdk
build.cs içerisinde **"OnlineSubsystem"** ekliyoruz

https://docs.unrealengine.com/4.27/en-US/API/Plugins/OnlineSubsystem/IOnlineSubsystem/  ---> koda ekleme
https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/Online/#interfaces     ---> inceleme

**instance** dosyasında **init** kısmında
```cpp
	IOnlineSubsystem* SubSystem = IOnlineSubsystem::Get();
	if (SubSystem != nullptr)
	{
		UE_LOG(LogTemp, Warning, TEXT("Found subsystem %s"), *SubSystem->GetSubsystemName().ToString());
	}else
	{
		UE_LOG(LogTemp, Warning, TEXT("Found no subsystem %s"));
	}
```
ile subsystem'imizi tanımlamış oluyoruz. Bunun sayesinde inceleme kısmında verdiğim linkte bulunan kısımlara erişimimiz oluyor.
**DefaultGameEngine.ini** dosyasına
```cpp
[OnlineSubsystem]
DefaultPlatformService=NULL
```
ekliyoruz.

![steamsub](./Pics/Unreal-Engine-C--.image28.png)

<h2>Create Session Interface</h2>

Subsystem null değer döndürmüyorsa oturumumuzu oluşturabiliriz bunun için:

```cpp
const IOnlineSessionPtr SessionInterface = SubSystem->GetSessionInterface();
		// check session is valid
		if(SessionInterface.IsValid())
		{
			UE_LOG(LogTemp, Warning, TEXT("Found session interface"));
		}
```
Stack is a linear data structure whereas Heap is a hierarchical data structure
https://docs.unrealengine.com/4.27/en-US/API/Runtime/Core/Templates/TSharedPtr/

**Not:** NULL subsystem geliştirme kısmında test etmemize yarar.

<h2>Create Session</h2>

https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/ProgrammingWithCPP/UnrealArchitecture/Delegates/Multicast/
https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/Online/SessionInterface/

Server cevap beklerken diğer işlemlerin de aynı şekilde yürümesi için delegate kullanıyoruz.

```cpp
void UPuzzlePlatformsGameInstance::OnCreateSessionComplete(FName SessionName, bool Succes)
void UPuzzlePlatformsGameInstance::OnDestroySessionComplete(FName SessionName, bool Succes)
```

şeklinde 2 delegate oluşturup eğer session yoksa oluşturuyoruz. Var ise oyunu kuruyoruz (host oluyoruz).

Delegate kısımlarında bunu hem kontrol hem oluşturma kısmını yaparken host butonuna bastığımızda çağırdığımız host fonksiyonu bunlardan hangisinin çağrılacağını belirliyor.
Yani direkt host fonksiyonunda interface doğruluğunu kontrol edip ardından session oluşturma-silme işlemini çağırıyoruz.

Steam bağlantısı için **steam online subsystem**’i aktif ediyoruz.
**Build.cs** içerisine **OnlineSubsystemSteam**’i ekliyoruz.
**DefaultEngine.ini** içerisinde **DefaultPlatformService** kısmını **Steam** yapıyoruz.

**TOptional:** Veriyi üye değişken olarak saklar.

**TSharedPtr:** Veriyi başka yerde saklar ve onu işaret eder.

OnlineSessionInterface ile bağlanma sıralaması:


<div class="mermaid">
graph LR
HostSession --> FindSession --> JoinSession --> GetResolvedConnectString --> ClientTravel
</div>


**Log LogOnline Verbose:** Online bağlanırken bunun log’unu yazdırır.

**Steam OSS bağlantısı için:** **Steam OSS Plugin – OnlineSubsystemSteam** modülü gerekli.
```cpp
SessionSettings.bUsesPresence = true;
```

Presence’yi aktif etme sebebimiz Steam lobilerini kullanmak için eğer kullanmazsak lan game olarak oynayabiliriz.

**-nonsteam** yazarak çalıştırırsak steam ihtiyaç duymadan test edebiliriz.

```cpp
// if you want to test NonSteam and Steam version, you need to add this conduction.
// for the online game, delete this conduction and add to only false version.
if (IOnlineSubsystem::Get()->GetSubsystemName() == "NULL")
{
   SessionSettings.bIsLANMatch = true; // local gameplay is true
}
else
{
   SessionSettings.bIsLANMatch = false; // local gameplay is false
}
```

![Image](./Pics/Unreal-Engine-C--._image5.png)

**Non-Seamless Travel:** Tüm kullanıcıları ilk önce oyundan çıkarır (disconnect) sonra tekrar bağlar. Bundan dolayı haritayı yüklerken oyun donar ve yüklenince tekrar akıcılaşır.

**Seamless Travel:** Kullanıcıları alıp tekrar yüklerken geçici  (transition) map kullanır bu sayede donma yaşanmaz. Bu geçiş haritası 2 büyük harita arasında yükleme yaparken bir loading map veya küçük harita olabilir. Aktif etmek için: 
```cpp
 bUseSeamlessTravel = true; 
```
kullanılır.

![Image](./Pics/Unreal-Engine-C--.image11.png)

```cpp
UFUNCTION(NetMulticast, unreliable, WithValidation)
```

Validation kısmı oyuncunun gönderidiği değerin null olmaması için işlemi keserek önlem sağlıyor.
Unreliable kısmı uzaktan müdahale edilmesini engellemek için.

**NetMultiCast:** suncudan bilgiyi alıp tüm oyunculara dağıtmak için genel kullanım.

**Server:** bilgi client’de çağrılacak fakat suncuda yürütülecek.

**Client:** Sunucudan bilgiyi alıp client’de yürütme işlemini yapar.

**Not:** Bunu herhangi bir fonksiyona ekledikten sonra o fonksiyonun _Implementation
Tipini de yapmamız gerekiyor. Bunun sayesinde iletişimi sağlamış oluyoruz.

**Not2:** Validation işlemi için de _Validate gerekiyor. Aynı zamanda bunları yapmak hile koruması da sağlar.
Server içerisinde ki görevimizi (rol) görmek için:

```cpp
FString GetEnumText(ENetRole Role)
{
   switch (Role)
   {
   case ROLE_None:
         return "None";
   case ROLE_Authority:
      return "Authority";
      
   case ROLE_AutonomousProxy:
      return "AutonomousProxy";
   case ROLE_SimulatedProxy:
      return "SimulatedProxy";
   default:
      return "ERROR";
   }
}

DrawDebugString(
   GetWorld(), // world space
   FVector(0,0,100), // start vector
   GetEnumText(GetLocalRole()), // end vector
   this,
   FColor::White, // color
   DeltaSeconds
);
```

Değerlerin replicate edilmesi için:
Öncelikle aktörün replikasyonunu açıyoruz bunun için **constructor** kısmına
```cpp
bReplicates = true;
```
ardından değeri ve replicated fonk. yazıyoruz.

```cpp
UPROPERTY(ReplicatedUsing = OnRep_ReplicatedTransform)
FTransform ReplicatedTransform;

void AsquidgameCharacter::OnRep_ReplicatedTransform()
{
   //UE_LOG(LogTemp, Warning, TEXT("Replicated Transform"));
   SetActorTransform(ReplicatedTransform);
}

void AsquidgameCharacter::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
   Super::GetLifetimeReplicatedProps(OutLifetimeProps);

   DOREPLIFETIME(AsquidgameCharacter, ReplicatedTransform); // registering the value
}
```

<h2>Sunucu testi yaparken</h2>
Sanal bir şekilde packet loss, lag vb. şeyler için host olduğumuz oyunda konsolu açıp NetEmulation sonrasında

![Image](./Pics/Unreal-Engine-C--.image48.png)

birini kullanıyoruz.


# Puzzle Platform

Öncelikle platformu çalıştırdığımızda StaticMeshActor’un ne yapacağını yazıyoruz.

**MovingPlatform.h**

```cpp
public:
    AMovingPlatform();
    
    void MovementActive();
    void MovementDeactive();
 
private:
    void Movement(float DeltaTime);
    
    // MakeEditWidget command is working for the set location with visual (gizmo)
    UPROPERTY(EditAnywhere, Category= "Movement Settings", meta = (MakeEditWidget = true))
    FVector TargetLocation;
    FVector GlobalTargetLocation;
    FVector GlobalStartLocation;
    UPROPERTY(EditAnywhere, Category= "Movement Settings")
    float Speed = 20.0f;
    UPROPERTY(EditAnywhere)
    int ActivatePlatforms = 1;
 
protected:
 
    virtual void Tick(float DeltaTime) override;
    virtual void BeginPlay() override;
```

**MovingPlatform.cpp**

```cpp
AMovingPlatform::AMovingPlatform()
{
    PrimaryActorTick.bCanEverTick = true;
 
    // set platform movable object
    SetMobility(EComponentMobility::Movable);
}
 
void AMovingPlatform::BeginPlay()
{
    Super::BeginPlay();
 
    if (HasAuthority())
    {
        // it's opening replicate with movement
        SetReplicates(true);
        SetReplicateMovement(true);
    }
 
    GlobalStartLocation = GetActorLocation();
    GlobalTargetLocation = GetTransform().TransformPosition(TargetLocation);
}
 
void AMovingPlatform::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
 
    if (ActivatePlatforms > 0)
    {
        // check if is true, it's server. Else, it's client
        if (HasAuthority())
        {
            Movement(DeltaTime);
        }
    }
}
 
void AMovingPlatform::Movement(float DeltaTime)
{
    FVector Location = GetActorLocation();
    if ((GlobalTargetLocation - Location).IsUnit(Speed * DeltaTime))
    {
        Swap(GlobalTargetLocation, GlobalStartLocation);
    }
    FVector Direction = (GlobalTargetLocation - GlobalStartLocation).GetSafeNormal();
    Location += Speed * DeltaTime * Direction;
    SetActorLocation(Location);
}
 
void AMovingPlatform::MovementActive()
{
    ActivatePlatforms++;
}
 
void AMovingPlatform::MovementDeactive()
{
    if (ActivatePlatforms > 0)
    {
        ActivatePlatforms--;
    }
}
```

Ne yapacağını bildiğimize göre bunu etkinleştirmesi için bir AActor yaratıyoruz.

**PlatformTrigger.h**

```cpp
public:
    // Sets default values for this actor's properties
    APlatformTrigger();
 
private:
    UPROPERTY(EditAnywhere)
    class UBoxComponent* Trigger;
    
    UFUNCTION()
    void OnOverlapBegin(class UPrimitiveComponent* OverlappedComp, class AActor* OtherActor,
                        class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
                        const FHitResult& SweepResult);
    UFUNCTION()
    void OnOverlapEnd(class UPrimitiveComponent* OverlappedComp, class AActor* OtherActor,
                      class UPrimitiveComponent* OtherComp, int32 OtherBodyIndex);
    // find all platform on scene and select on editor
    UPROPERTY(EditAnywhere)
    TArray<class AMovingPlatform*> TriggerToPlatform;
 
protected:
    // Called when the game starts or when spawned
    virtual void BeginPlay() override;
    // Called every frame
    virtual void Tick(float DeltaTime) override;
```

**PlatformTrigger.cpp**

```cpp
// Sets default values
APlatformTrigger::APlatformTrigger()
{
    // Set this actor to call Tick() every frame.  You can turn this off to improve performance if you don't need it.
    PrimaryActorTick.bCanEverTick = true;
 
    Trigger = CreateDefaultSubobject<UBoxComponent>(TEXT("Trigger"));
    if (!ensure(Trigger != nullptr)) return;
    RootComponent = Trigger;
 
    Trigger->OnComponentBeginOverlap.AddDynamic(this, &APlatformTrigger::OnOverlapBegin);
    Trigger->OnComponentEndOverlap.AddDynamic(this, &APlatformTrigger::OnOverlapEnd);
}
 
// Called when the game starts or when spawned
void APlatformTrigger::BeginPlay()
{
    Super::BeginPlay();
}
 
// Called every frame
void APlatformTrigger::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
}
 
void APlatformTrigger::OnOverlapBegin(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
                                      UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
                                      const FHitResult& SweepResult)
{
    if (GEngine)
        GEngine->AddOnScreenDebugMessage(-1, 3.0f, FColor::Yellow, TEXT("Activated"));
    // you can do with int i value but this way more easier than.
    for (AMovingPlatform* Platform : TriggerToPlatform)
    {
        // Calling other page function
        Platform->MovementActive();
    }
}
 
void APlatformTrigger::OnOverlapEnd(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
                                    UPrimitiveComponent* OtherComp, int32 OtherBodyIndex)
{
    if (GEngine)
        GEngine->AddOnScreenDebugMessage(-1, 3.0f, FColor::Yellow, TEXT("Deactivated"));
    for (AMovingPlatform* Platform : TriggerToPlatform)
    {
        Platform->MovementDeactive();
    }
}
```
Platformun üzerine geldiğimiz zaman çalışmaya, bıraktığımız zaman ise durmaya başlayacak.

# Multiplayer lobby (Multiplayer with hamachi or any IP address)

Aşağıdaki gibi bir widget oluşturuyoruz.

![mainmenu](./Pics/Unreal-Engine-C--.image43.png)

Join butonuna basıldığında widget’lar arasında geçiş yapabilmek için WidgetSwitcher kullanıyoruz ve 2. Widget’ımızı aynı blueprint içerisinde oluşturuyoruz.

![join](./Pics/Unreal-Engine-C--.image46.png)

Sayfalar arasında veriyi iletebilmek için Inferface yaratıyoruz.

**Interface.h**

```cpp
public:
    // pure virtual function - no implementation
    virtual void Host() = 0;
    virtual void Join(const FString& Adress) = 0;
    virtual void LoadMainMenu() = 0;
```
.cpp dosyasına herhangi bir tanımlama yapmamıza gerek yok.
Oyun içinden de aynı şekilde main menu’ye gitmek için widget oluşturuyoruz.

![ingamemenu](./Pics/Unreal-Engine-C--.image45.png)

Artık butonlara tıklayabilmek ve leveller arası geçiş yaparken widget’ların açık kalmamasını sağlamak kaldı. Bunu yapmak için UUserWidget tanımlıyoruz.

**MenuWidget.h**

```cpp
public:
    void Setup();
    void SetMenuInterface(IMenuInterface* MenuInterface);
protected:
    IMenuInterface* MainMenuInterface = nullptr;
    APlayerController* PlayerController = nullptr;
    FInputModeUIOnly InputModeData; // call the struct
    FInputModeGameOnly InputModeGameOnly; // call the struct
 
protected:
    virtual void OnLevelRemovedFromWorld(ULevel* InLevel, UWorld* InWorld) override; // delete widget and set controller
```

**MenuWidget.cpp**

```cpp
#include "MenuWidget.h"
 
// Add default functionality here for any IMenuInterface functions that are not pure virtual.
void UMenuWidget::SetMenuInterface(IMenuInterface* MenuInterface)
{
    MainMenuInterface = MenuInterface;
}
 
// show widget on the screen
void UMenuWidget::Setup()
{
    // Show menu widget
    this->AddToViewport();
 
    UWorld* World = GetWorld();
    if (!ensure(World != nullptr)) { return; }
 
    // Show cursor on the menu for the click to button.
    PlayerController = World->GetFirstPlayerController();
    if (!ensure(PlayerController != nullptr)) return;
    //PlayerController->SetShowMouseCursor(true); // alternative for the show cursor
 
    InputModeData.SetWidgetToFocus(this->TakeWidget()); // show in the widget
    InputModeData.SetLockMouseToViewportBehavior(EMouseLockMode::DoNotLock); // set lock mode
 
    PlayerController->SetInputMode(InputModeData);
    PlayerController->bShowMouseCursor = true;
}
 
// When switched the level, it will activate input and remove the widget.
void UMenuWidget::OnLevelRemovedFromWorld(ULevel* InLevel, UWorld* InWorld)
{
    this->RemoveFromViewport();
 
    UWorld* World = GetWorld();
    if (!ensure(World != nullptr)) { return; }
 
    PlayerController = World->GetFirstPlayerController(); // call player controller
    if (!ensure(PlayerController != nullptr)) return;
 
    PlayerController->SetInputMode(InputModeGameOnly);
    PlayerController->bShowMouseCursor = false;
}
```

Şeklinde tanımlamalarımızı yaparak artık widget’lara tıklarken ki mouse gözükmeme ve leveller arasında geçiş yaparken yaşanan sorunları da önlemiş oluyoruz.

Artık butonlara bastığımız zaman gerekli işlemleri çalıştırmasını sağlamak kaldı bunun için oluşturduğumuz MenuWidget’ın alt sınıfını oluşturuyoruz.
```
UMainMenu : public UMenuWidget
```

**MainMenu.h**

```cpp
private:
    // Looks for a widget in Blueprint with the same name as the property.
    UPROPERTY(meta = (BindWidget))
    class UButton* Host; // host button in main menu
    UPROPERTY(meta = (BindWidget))
    class UButton* Join; // join button in main menu
    UPROPERTY(meta = (BindWidget))
    class UButton* JoinButton; // join button in join a game menu
    UPROPERTY(meta = (BindWidget))
    class UButton* Back; // back button in main menu
    UPROPERTY(meta = (BindWidget))
    class UButton* ExitButton; // back button in main menu
    UPROPERTY(meta = (BindWidget))
    class UWidgetSwitcher* WidgetSwitch; // wrap with widget switcher on the editor
    UPROPERTY(meta = (BindWidget))
    class UWidget* JoinMenu; // join menu widget
    UPROPERTY(meta = (BindWidget))
    class UEditableTextBox* IPWriteBox; // ip write box on join menu widget
 
    UFUNCTION()
    void HostServer(); // when you pushed the button, it will call this func.
    UFUNCTION()
    void JoinServer();
    UFUNCTION()
    void OpenJoinMenu(); // when you pushed the button, it will call this func.
    UFUNCTION()
    void Exit();
 
protected:
    virtual bool Initialize() override;
```

**MenuWidget.cpp**

```cpp
#include "MainMenu.h"
#include "Components/Button.h"
#include "Components/WidgetSwitcher.h"
#include "Components/EditableTextBox.h"
 
bool UMainMenu::Initialize()
{
    bool Success = Super::Initialize();
    if (!Success) { return false; }
    if (!ensure(Host != nullptr)) { return false; }
    Host->OnClicked.AddDynamic(this, &UMainMenu::HostServer);
    if (!ensure(Join != nullptr)) { return false; }
    Join->OnClicked.AddDynamic(this, &UMainMenu::OpenJoinMenu);
    if (!ensure(Back != nullptr)) { return false; }
    Back->OnClicked.AddDynamic(this, &UMainMenu::OpenJoinMenu);
    if (!ensure(JoinButton != nullptr)) { return false; }
    JoinButton->OnClicked.AddDynamic(this, &UMainMenu::JoinServer);
    if (!ensure(ExitButton != nullptr)) { return false; }
    ExitButton->OnClicked.AddDynamic(this, &UMainMenu::Exit);
    return true;
}
 
// host the server
void UMainMenu::HostServer()
{
    if (MainMenuInterface != nullptr)
    {
        MainMenuInterface->Host();
    }
}
// connect - join the server
void UMainMenu::JoinServer()
{
    if(MainMenuInterface != nullptr && IPWriteBox != nullptr)
    {
        MainMenuInterface->Join(IPWriteBox->GetText().ToString());
    }
}
// switch between main menu
void UMainMenu::OpenJoinMenu()
{
    if (!ensure(WidgetSwitch != nullptr)) { return; }
    if (!ensure(JoinMenu != nullptr)) { return; }
    
    if (WidgetSwitch->GetActiveWidget() == JoinMenu)
    {
        WidgetSwitch->SetActiveWidget(this); // this = main menu - widget 0 index
    }
    else
    {
        // you can active this method (it's safety) or you can active with widget index.
        WidgetSwitch->SetActiveWidget(JoinMenu);
    }
}
// Exit to the game
void UMainMenu::Exit()
{
    UWorld* World = GetWorld();
    if (!ensure(World != nullptr)) { return; }
 
    PlayerController = World->GetFirstPlayerController(); // call player controller
    if (!ensure(PlayerController != nullptr)) return;
 
    PlayerController->ConsoleCommand("quit");
}
```
Bu şekilde tanımlamaları yaparak widget içerisine koyduğumuz her şeye işlevini kazandırmış oluyoruz.
Not: Widget içerisinde bulunan isimleri ile fonksiyonun ismi aynı olmak zorunda.
Aynı şekilde hemen PauseMenu de yaratıyoruz.


**PauseMenu.h**

```cpp
UCLASS()
class YourGAME_API UPauseMenu : public UMenuWidget
{
    GENERATED_BODY()
 
public:
    UPROPERTY(meta = (BindWidget))
    class UButton* CancelButton;
    UPROPERTY(meta = (BindWidget))
    class UButton* QuitButton;
 
private:
    UFUNCTION()
    void Quit();
    UFUNCTION()
    void Cancel();
 
protected:
    virtual bool Initialize() override;
};
```

**PauseMenu.cpp**

```cpp
bool UPauseMenu::Initialize()
{
    bool Success = Super::Initialize();
    if (!Success) { return false; }
    if (!ensure(CancelButton != nullptr)) { return false; }
    CancelButton->OnClicked.AddDynamic(this, &UPauseMenu::Cancel);
    if (!ensure(QuitButton != nullptr)) { return false; }
    QuitButton->OnClicked.AddDynamic(this, &UPauseMenu::Quit);
 
    return true;
}
 
void UPauseMenu::Quit()
{
    if(MainMenuInterface != nullptr)
    {
        OnLevelRemovedFromWorld(GetWorld()->GetCurrentLevel(),GetWorld()); // show main menu widget and set cursor
        MainMenuInterface->LoadMainMenu(); // travel to the main menu map
    }
}
 
void UPauseMenu::Cancel()
{
    OnLevelRemovedFromWorld(GetWorld()->GetCurrentLevel(),GetWorld()); // clear viewport and get controller
}
```

Artık butonlar ile verdiğimiz emir ile haritalar arası geçiş yaparken, hem geçiş yapmamızı hem de bilgileri taşımamıza yarayan Instance oluşturuyoruz.

**GameInstance.h**

```cpp
UCLASS()
class YourGAME_API UYourGameInstance : public UGameInstance, public IMenuInterface
{
    GENERATED_BODY()
 
public:
    UYourGameInstance(const FObjectInitializer& ObjectInitializer);
 
    virtual void Init() override;
 
    UFUNCTION(BlueprintCallable)
    void LoadMenu();
    UFUNCTION(BlueprintCallable)
    void PauseLoadMenu();
 
    UFUNCTION(Exec) // exec = console command
    virtual void Host() override;
 
    UFUNCTION(Exec)
    virtual void Join(const FString& Adress) override;
 
    virtual void LoadMainMenu() override;
 
private:
    TSubclassOf<class UUserWidget> MenuClass = nullptr;
    UMainMenu* Menu = nullptr;
    TSubclassOf<class UUserWidget> PauseMenuClass = nullptr;
    UPauseMenu* PauseMenu = nullptr;
};
```


**GameInstance.cpp**

```cpp
UYourGameInstance::UYourGameInstance(const FObjectInitializer& ObjectInitializer)
{
    // find menu widget class and define to MenuClass.
    ConstructorHelpers::FClassFinder<UUserWidget> MenuBPClass(TEXT("/Game/Blueprints/Widgets/WBP_MainMenu"));
    if (!ensure(MenuBPClass.Class != nullptr)) return;
    MenuClass = MenuBPClass.Class;
    // find pause menu widget class and define to PauseMenuClass.
    ConstructorHelpers::FClassFinder<UUserWidget> PauseMenuBPClass(TEXT("/Game/Blueprints/Widgets/WBP_PauseMenu"));
    if (!ensure(PauseMenuBPClass.Class != nullptr)) return;
    PauseMenuClass = PauseMenuBPClass.Class;
    //UE_LOG(LogTemp, Warning, TEXT("Game Instance Constructor"));
}
 
void UYourGameInstance::Init()
{
    //UE_LOG(LogTemp, Warning, TEXT("Game Instance Init"));
    
}
 
void UYourGameInstance::Host()
{
    // load to the map
    UWorld* World = GetWorld();
    if (!ensure(World != nullptr)) { return; }
    World->ServerTravel(TEXT("/Game/Maps/Level2?listen")); // ?listen server
}
 
void UYourGameInstance::Join(const FString& Adress)
{
    if (GEngine)
        GEngine->AddOnScreenDebugMessage(-1, 15.0f, FColor::Yellow, FString::Printf(TEXT("Joining %s"), *Adress));
    // join to the input adress.
    APlayerController* PlayerController = GetFirstLocalPlayerController();
    if (!ensure(PlayerController != nullptr)) { return; }
    PlayerController->ClientTravel(Adress, ETravelType::TRAVEL_Absolute);
}
// create main menu widget
void UYourGameInstance::LoadMenu()
{
    // Create menu widget
    if (!ensure(MenuClass != nullptr)) return;
    Menu = CreateWidget<UMainMenu>(this, MenuClass); // create menu widget with MenuClass
    if (!ensure(Menu != nullptr)) return;
 
    Menu->Setup(); // add to viewport and show cursor
    Menu->SetMenuInterface(this);
}
// create pause menu widget
void UYourGameInstance::PauseLoadMenu()
{
    if (!ensure(PauseMenuClass != nullptr)) return;
    PauseMenu = CreateWidget<UPauseMenu>(this, PauseMenuClass); // create menu widget with MenuClass
    if (!ensure(PauseMenu != nullptr)) { return; }
    PauseMenu->Setup(); // add to viewport and show cursor
    PauseMenu->SetMenuInterface(this);
}
// open the main menu
void UYourGameInstance::LoadMainMenu()
{
    APlayerController* PlayerController = GetFirstLocalPlayerController();
    if (!ensure(PlayerController != nullptr)) { return; }
    PlayerController->ClientTravel("/Game/Maps/MainMenu", ETravelType::TRAVEL_Absolute); return to the main menu
}
```

<h1> Let’s visualize the menu system</h1>

![menusystem](./Pics/Unreal-Engine-C--.image33.png)
![menusystem2](./Pics/Unreal-Engine-C--.image8.jpg)
![Image](./Pics/Unreal-Engine-C--.image60.png)
![Image](./Pics/Unreal-Engine-C--.image54.png)
![Image](./Pics/Unreal-Engine-C--.image25.png)

Artık tüm işlemlerimizi halletiğimize göre birisi host olup diğeri onun vereceği IP adresine bağlanabilir.
Ör: https://www.youtube.com/watch?v=vXsprCUXH68

# Character Control 

Character oluşturduktan sonra **.h** file dosyasına:
```cpp
class UGrabber;
protected:
UFUNCTION(BlueprintImplementableEvent, BlueprintPure)
UGrabber* GetGrabber() const;
Public:
UGrabber* GetGrabber = nullptr;
private:
void Forward(float AxisValue);
void Right(float AxisValue);
void Grab();
void Release();
```

ekledikten sonra **.cpp** dosyasına:
```cpp
#include "Components/InputComponent.h"
#include "Grabber.h"
#include "GameFramework/CharacterMovementComponent.h"
// Called to bind functionality to input
void AFirstPersonCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
Super::SetupPlayerInputComponent(PlayerInputComponent);
// key mapping all functions.
PlayerInputComponent->BindAxis(TEXT("Forward"), this, &AFirstPersonCharacter::Forward);
PlayerInputComponent->BindAxis(TEXT("Right"), this, &AFirstPersonCharacter::Right);
PlayerInputComponent->BindAxis(TEXT("LookUp"), this, &APawn::AddControllerPitchInput);
PlayerInputComponent->BindAxis(TEXT("LookRight"), this, &APawn::AddControllerYawInput);
PlayerInputComponent->BindAction(TEXT("Jump"), EInputEvent::IE_Pressed, this, &ACharacter
::Jump);
PlayerInputComponent->BindAction(TEXT("Grab"), EInputEvent::IE_Pressed, this, &AFirstPers
onCharacter::Grab);
PlayerInputComponent->BindAction(TEXT("Grab"), EInputEvent::IE_Released, this, &AFirstPer
sonCharacter::Release);
}
void AFirstPersonCharacter::Forward(float AxisValue)
{
// movement for forward or backward.
GetCharacterMovement()->AddInputVector(GetActorForwardVector() * AxisValue);
}
void AFirstPersonCharacter::Right(float AxisValue)
{
// movement for right or left.
GetCharacterMovement()->AddInputVector(GetActorRightVector() * AxisValue);
}
void AFirstPersonCharacter::Grab()
{
// calling grab component in grab function.
GetGrabber->Grab();
}
void AFirstPersonCharacter::Release()
{
// calling grab component in release function.
GetGrabber->Release();
}
```

# C++ to Blueprint Node 

Blueprint pure: No node function in bp
```
UFUNCTION(BlueprintCallable, BlueprintPure)
FVector GetMaxGrabLocation() const;
```
![BlueprintPure](./Pics/Unreal-Engine-C--.image1.png)

Blueprint pure: No node function in bp
```
UFUNCTION(BlueprintImplementableEvent)
void NotifyQuestActor(AActor* Actor);
```
![BlueprintImplementableEvent](./Pics/Unreal-Engine-C--.image2.png)
