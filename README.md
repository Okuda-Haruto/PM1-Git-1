#include <Novice.h>
#include <math.h>
#include<Vector2.h>

const char kWindowTitle[] = "イージング(ペアプログラミング)";

// Windowsアプリでのエントリーポイント(main関数)
int WINAPI WinMain(HINSTANCE, HINSTANCE, LPSTR, int) {

	// ライブラリの初期化
	Novice::Initialize(kWindowTitle, 1280, 720);

	// キー入力結果を受け取る箱
	char keys[256] = { 0 };
	char preKeys[256] = { 0 };

	struct Circle {
		Vector2 pos = { 50,100 };
		int radius = 50;
	};
	Circle circle[4];
	for (int i = 0; i < 4; i++) {
		circle[i].pos.y = i * 100.0f + 100.0f;
	}
	float flame = 0;

	// ウィンドウの×ボタンが押されるまでループ
	while (Novice::ProcessMessage() == 0) {
		// フレームの開始
		Novice::BeginFrame();

		// キー入力を受け取る
		memcpy(preKeys, keys, 256);
		Novice::GetHitKeyStateAll(keys);

		///
		/// ↓更新処理ここから
		///

		flame += 0.01f;
		if (flame > 1.0f) {
			flame = 1.0f;
		}

		if (keys[DIK_R]) {
			flame = 0.0f;
		}

		for (int i = 0; i < 4; i++) {
			switch (i) {
			case 0: // ノーマル
				circle[i].pos.x = 50.0f * (1.0f - flame) + 1230.0f * flame;
				break;

			case 1:
				circle[i].pos.x = 50.0f * (1.0f - powf(flame, 2.0f)) + 1230.0f * powf(flame, 2.0f);
				break;

			case 2:
				if (flame < 0.5f) {
					circle[i].pos.x = 50.0f * (1.0f - 2.0f * powf(flame, 2.0f)) + 1230.0f * (2.0f * powf(flame, 2.0f));
				} else {
					circle[i].pos.x = 50.0f * powf(2.0f - flame * 2.0f, 2.0f) / 2 + 1230.0f * (1.0f - powf(2.0f - flame * 2.0f, 2.0f) / 2);
				}
				break;

			case 3:
				circle[i].pos.x = 50.0f * powf(1.0f - flame, 2.0f) + 1230.0f * (1.0f - powf(1.0f - flame, 2.0f));
				break;
			}
		}

		///
		/// ↑更新処理ここまで
		///

		///
		/// ↓描画処理ここから
		///

		for (int i = 0; i < 4; i++) {
			Novice::DrawEllipse(int(circle[i].pos.x), int(circle[i].pos.y), int(circle[i].radius), int(circle[i].radius), 0.0f, 0xFF2222FF, kFillModeSolid);
		}

		///
		/// ↑描画処理ここまで
		///

		// フレームの終了
		Novice::EndFrame();

		// ESCキーが押されたらループを抜ける
		if (preKeys[DIK_ESCAPE] == 0 && keys[DIK_ESCAPE] != 0) {
			break;
		}
	}

	// ライブラリの終了
	Novice::Finalize();
	return 0;
}
